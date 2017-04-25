---
title: PHP用Trie树 实现敏感词过滤
date: 2016-06-08 14:50:52
tags: 敏感词 Trie树
categories: PHP
---
之前做了一个发射弹幕的功能，有一个需求，是要将用户发射的敏感弹幕给屏蔽掉。第一次做这种功能，于是在网上搜罗了一些例，看到PHP用Trie树实现敏感词过滤是比较通用的一种办法。在这里简单记录下实现的思路吧。
<!-- more -->
### 解决字符集的问题
{% codeblock lang:php  %}
/**
 * Desc:分词的方法 将字符串拆分数组
 * @Param:$utf8_str  字符串
 * @Return:Array
 * */
public function getChars($utf8_str){
	$s = $utf8_str;
	$len = strlen($s);
	if($len == 0) return array();
	$chars = array();
	for($i = 0;$i < $len;$i++){
		$c = $s[$i];
		$n = ord($c);
		//0xxx xxxx, asci, single
		if(($n >> 7) == 0){	
			$chars[] = $c;
		}
		//1111 xxxx, first in four char
		else if(($n >> 4) == 15){ 
			if($i < $len - 3){
				$chars[] = $c.$s[$i + 1].$s[$i + 2].$s[$i + 3];
				$i += 3;
			}
		}
		//111x xxxx, first in three char
		else if(($n >> 5) == 7){ 
			if($i < $len - 2){
				$chars[] = $c.$s[$i + 1].$s[$i + 2];
				$i += 2;
			}
		}
		//11xx xxxx, first in two char
		else if(($n >> 6) == 3){ 
			if($i < $len - 1){
				$chars[] = $c.$s[$i + 1];
				$i++;
			}
		}
	}
	return $chars;
}
{% endcodeblock %}

### 用Trie树实现敏感词树
字符单位确认以后，然后实现Trie树了。
简单的算法，从根路径开始给每个字符建一个关联数组，当字符串结束的时候，用一个null表示结尾！将敏感词列表以这样的关系存入字典树中。匹配是否包含敏感词的话，一直根据字符在字典树中一直找到null为止就表明存在，任一字符不存在就表明串不存在~
因为PHP没有全局缓存的机制，每次都要从数据库或者redis中读取全部的敏感词，然后建立Trie树再去匹配串的话太麻烦了，采取的办法是将Trie内部的关联数组序列化后直接保存在数据库中，每次只要读取这条数据，然后反序列化，Trie树就回来了。当然进行串的插入和删除，将更新这个序列化数据。

{% codeblock lang:php  %}
<?php
define('BLACK_WORDS','childday_black_words');
class FilterWords{

    private $tree = array();

	/**
	 * Desc:插入敏感词
	 * @Param:$utf8_str  需要添加的敏感词
	 * */
	public function insert($utf8_str){
		$chars = $this->get_chars($utf8_str);
		$chars[] = null;	//串结尾字符
		$count = count($chars);
		$T = &$this->tree;
		for($i = 0;$i < $count;$i++){
			$c = $chars[$i];
			if(!array_key_exists($c, $T)){
				$T[$c] = array();	//插入新字符，关联数组
			}
			$T = &$T[$c];
		}
	}


	/**
	 * Desc:从列表中删除敏感词
	 * @Param:$utf8_str  需要删除的敏感词
	 * */
	public function remove($utf8_str){
		$chars = &$this->get_chars($utf8_str);
		$chars[] = null;
		if($this->_find($chars)){ //先保证此串在树中
			$chars[] = null;
			$count = count($chars);
			$T = &$this->tree;
			for($i = 0;$i < $count;$i++){
				$c = $chars[$i];
				if(count($T[$c]) == 1){ //表明仅有此串
					unset($T[$c]);
					return;
				}
				$T = &$T[$c];
			}
		}
	}


	/**
	 * Desc:验证是否包含敏感词
	 * @Param:$utf8_str  需要验证的字符串
	 * @Param:$do_count  是否统计敏感词出现的次数
	 * @Return:boolean
	 * */
	public function contain($utf8_str, $do_count = 0){
		$chars = $this->get_chars($utf8_str);
		$chars[] = null;
		$len = count($chars);
		$Tree = &$this->tree;
		$count = 0;
		for($i = 0; $i < $len; $i++){
			$c = $chars[$i];
			if(array_key_exists($c, $Tree)){	//起始字符匹配
				$T = &$Tree[$c];
				for($j = $i + 1;$j < $len;$j++){
					$c = $chars[$j];
					if(array_key_exists(null, $T)){
						if($do_count){
							$count++;
						}
						else{
							return true;
						}
					}
					if(!array_key_exists($c, $T)){
						break;
					}
					$T = &$T[$c];
				}
			}
		}
		if($do_count){
			return $count;
		}
		else{
			return false;
		}
	}


	public function contain_all($str_array){
		foreach($str_array as $str){
			if($this->contain($str)){
				return true;
			}
		}
		return false;
	}

	private function _find(&$chars){
		$count = count($chars);
		$T = &$this->tree;
		for($i = 0;$i < $count;$i++){
			$c = $chars[$i];
			if(!array_key_exists($c, $T)){
				return false;
			}
			$T = &$T[$c];
		}
		return true;
	}


	/**
	 * Desc:初始化敏感词树
	 */
	public function import($str){
		$this->tree = unserialize($str);
	}

	/**
	 * Desc:更新敏感词列表
	 */
	public function export(){
		return serialize($this->tree);
	}


	/**
	 * Desc:分词的方法 将字符串拆分数组
	 * @Param:$utf8_str  字符串
	 * @Return:Array
	 * */
	public function get_chars($utf8_str){
		$s = $utf8_str;
		$len = strlen($s);
		if($len == 0) return array();
		$chars = array();
		for($i = 0;$i < $len;$i++){
			$c = $s[$i];
			$n = ord($c);
			if(($n >> 7) == 0){		//0xxx xxxx, asci, single
				$chars[] = $c;
			}
			else if(($n >> 4) == 15){ 	//1111 xxxx, first in four char
				if($i < $len - 3){
					$chars[] = $c.$s[$i + 1].$s[$i + 2].$s[$i + 3];
					$i += 3;
				}
			}
			else if(($n >> 5) == 7){ 	//111x xxxx, first in three char
				if($i < $len - 2){
					$chars[] = $c.$s[$i + 1].$s[$i + 2];
					$i += 2;
				}
			}
			else if(($n >> 6) == 3){ 	//11xx xxxx, first in two char
				if($i < $len - 1){
					$chars[] = $c.$s[$i + 1];
					$i++;
				}
			}
		}
		return $chars;
	}

	/**
	 * Desc:当redis 缓存不存在时,读取数据库中敏感词到字典树和redis缓存中
	 *
	 * */
	public function  importBlackWords(){
		$ci =& get_instance();
		$ci->load->database();
		$ci->db->select('word');
		$data = $ci->db->get('childday_black_words')->result_array();

		foreach ($data as $v){
			$this->insert($v['word']);

		}
		return $this->export();
	}

}

//$filter_words_obj = new FilterWords();
{% endcodeblock %}

简单的敏感词过滤的类就是实现啦，也不算是很难~

