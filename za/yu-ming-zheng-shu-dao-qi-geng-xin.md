# 域名证书到期，更新

域名证书到期，到godaddy对应域名下载新证书，因为使用的是nginx 所以下载类别选其他 证书文件 dfe616168a014563.crt dfe616168a014563.pem gd\_bundle-g2-g1.crt 生成新的crt `cat dfe616168a014563.crt gd_bundle-g2-g1.crt > lwork.crt` 使用该crt替换老的crt

