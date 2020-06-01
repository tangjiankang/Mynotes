# awk

### ==&gt;&gt; 以下两种方法可以达到同样的效果

### awk -F " " '{print $2}' cnyunwei.log \| sort -r \| uniq -c \| grep -v "1 "

### awk -F " " '{print $2}' cnyunwei.log \| sort -r \| uniq -c \| awk '{if\($1&gt;1\){print $0}}'

awk -F " " '{print $2}' cnyunwei.log \| sort -r \| uniq -c **意思是提取出第二列并过滤重复，且列出重复行数**

扩展一下，把以上结果所在行整行内容取出==&gt;&gt; 把上面取出的结果临时存于temp.log文件中，再读取这个文件来取原文件里的整行内容

awk -F " " '{print $2}' cnyunwei.log \| sort -r \| uniq -c \| grep -v "1 " \| awk '{print $2}' &gt;&gt; temp.log 或 awk -F " " '{print $2}' cnyunwei.log \| sort -r \| uniq -c \| awk '{if\($1&gt;1\){print $0}}' \| awk '{print $2}' &gt;&gt; temp.log ==&gt;&gt; vi cnyunwei.sh

```text
#!/bin/sh
SOCFILENAME=cnyunwei.log
FILENAME=temp.log

if [ -e $FILENAME ]; then
rm -rf $FILENAME
fi
awk -F " " '{print $2}' $SOCFILENAME | sort -r | uniq -c | grep -v "1 " | awk '{print $2}' >> $FILENAME

while read LINE
do
  grep $LINE $SOCFILENAME
done < $FILENAME
exit 0
```

更简单的方法合并成一行命令搞定： awk -F " " '{print $2}' cnyunwei.log \| sort -r \| uniq -c \| awk '{if\($1&gt;1\){print $0}}' \| awk '{print $2}' \| while read output;do grep $output cnyunwei.log; done

## cat 1.txt \|awk -F, '{print $2,$1}'

