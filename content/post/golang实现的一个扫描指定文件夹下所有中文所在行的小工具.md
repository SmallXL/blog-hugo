---
title: "Golang实现的一个扫描指定文件夹下所有中文所在行的小工具"
date: 2018-05-22T20:02:00+08:00
lastmod: 2018-05-22T20:02:00+08:00
draft: false
keywords: []
description: ""
tags: [golang]
categories: [golang小实践]
author: "xlxing"

# you can close something for this content if you open it in config.toml.
comment: false
toc: false
# you can define another contentCopyright. e.g. contentCopyright: "This is an another copyright."
contentCopyright: false
reward: false
mathjax: false
---

<!--more-->
**由于逻辑比较简单废话不多说直接上代码：**

```
package main

import (
	"bufio"
	"fmt"
	"io"
	"io/ioutil"
	"os"
	"strings"
	"unicode"
)

var write *os.File

func IsChineseChar(str string) bool {
	for _, r := range str {
		if unicode.Is(unicode.Scripts["Han"], r) {
			return true
		}
	}
	return false
}

func scanAll(path string) {
	files, _ := ioutil.ReadDir(path)
	for _, fi := range files {
		if fi.Name() != "chinese.txt" && !strings.Contains(fi.Name(), "XXX") {
			if fi.IsDir() {
				//过滤条件（某些文件夹不进入）
				if !strings.Contains(fi.Name(), "XXX") {
					scanAll(path + "/" + fi.Name())
				}
			} else {
				//只扫描go文件
				if strings.Contains(fi.Name(), ".go") {
					file, _ := os.Open(path + "/" + fi.Name())
					buffer := bufio.NewReader(file)
					flag := true
					for {
						s, _, ok := buffer.ReadLine()
						canWrite := true
						if strings.Contains(string(s), "//"){
							canWrite = false
						}
						if strings.Contains(string(s), "/*") {
							flag = false
						}
						if ok == io.EOF {
							break
						}
						if flag && canWrite && IsChineseChar(string(s)) {
							write.WriteString(string(s) + "\n")
						}
						if strings.Contains(string(s), "*/") {
							flag = true
						}
					}
					file.Close()
				}
			}
		}
	}
}

func main() {
	defer write.Close()
	var err error
	write, err = os.OpenFile("chinese.txt", os.O_RDWR|os.O_CREATE, 0766)
	if err != nil {
		fmt.Println("chinese.txt Read Err:", err)
		return
	}
	scanAll(".")
}
```
