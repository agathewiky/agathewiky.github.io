---
layout: post
title: BitComet私有的filehash算法
category: learning
---

最近在做p2p下载的一些相关项目，做下载加速，了解到BitComet有个私有的加速技术`LTseed`

标准的p2p是根据种子文件中数据块的SHA1值去DHT网络中寻找文件，它是把种子里的所有文件按照分块算法统一大小分块，再计算SHA1，即便是相同的文件，在不同的种子里，它计算出来的SHA1也是不一样的（当然也有可能一样，但概率比较小）。

`LTseed`技术就解决了这个问题，BitComet在种子文件里加了一个可选项filehash,用来标示一个完整的文件，不过这个filehash的算法BitComet并没有公开，然后就是这偏文章的重点。

此算法是熊长一大牛破解，我是辗转关系求教得之，大牛说可以写写放到网上分享出来，然后就有了这篇文章。

##Long-Term Seeding
> Long-Term Seeding is a unique feature of BitComet which helps users to get data from peers who have 100% of the torrent contents. 

当A将要下载一个文件，此时B正好已经下载完成了该文件，这个时候A就可以去B那里下载文件，当然前提是两个人都开启了此功能。

##LT-hash

`LT-hash`就是filehash

它是一个分段采样SHA1算法:

>文件size<=10M，计算全文sha1   
>size>10M,平均分10段，每段取1M,按顺序合并起来计算sha1

分段时有个对齐的问题，不能整分是多出来的字节归到最后一段

也就是这个样子的：
|1M|---|1M|---|1M|---|1M|---|1M|---|1M|---|1M|---|1M|---|1M|---|1M|

上代码：
	
	#!/usr/bin/env python
	#coding=utf-8
	def filehash(filename):
	    minsize = 1024 * 1024 * 10 #10M
	    k = 1024 * 1024 #1M
	    ch = 10
	    f = open(filename, 'rb')
	    f.seek(0, os.SEEK_END)
	    size = f.tell() #获取文件大小
	    f.seek(0)
	    sha = sha1() #初始化sha1
	    if size <= minsize:
	        #小于10M时，算全文sha1
	        data = f.read(size)
	        sha.update(data)
	    else:
	        tx = (size - minsize) / 9
	        chipsize = tx + k
	
	        print 'chipsize=',chipsize
	        for i in xrange(0,ch):
	            offset = chipsize * (i)
	            if i == 9:
	                offset = size - k
	            f.seek(offset)
	            data = f.read(k)
	            sha.update(data)
	
	    return sha.hexdigest()
	    
	    
##引用
* [Long-Term Seeding](http://wiki.bitcomet.com/long-term_seeding.)
* [bttorrent 种子文件结构解析](http://segmentfault.com/a/1190000000681331)
