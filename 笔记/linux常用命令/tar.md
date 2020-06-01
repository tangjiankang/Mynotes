### tar 本职只做归档(不压缩)，但可调用gzip对归档结果进行压缩
参数
-c 创建tar包  `tar zcf test.tar.gz 目标文件或目录`
-z 调用gzip
-j 调用 bzip2
-x 解包
-t 查看 `tar ztf test.tar.gz` 查看里面的文件
-C 指定解压位置
-f 使用归档文件
-- remove 打包后删除原文件
-- newer-mtime 2020-03-19备份该日期以后的