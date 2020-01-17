

# Installing WordPress on Ajenti V

## 1. Configuring WordPress

Truy cập Portal Ajenti vào mục Web -> Website (chỉ xuất hiện sau khi cài xong plugin Ajenti-V)
![Website](https://github.com/octvitasut/fWS/blob/master/common/ajenti/portal_ajenti.PNG)

Trên giao diện Web của Ajenti, nhập tên domain và ấn create Create để tạo mới domain cho Nginx

![Add_Domain](https://github.com/octvitasut/fWS/blob/master/common/ajenti/add_domain.PNG)

Với mỗi lần tạo một domain, 1 file config của domain cho nginx sẽ được khởi tạo tại đường dẫn `/etc/nginx/conf.d`

Để thay đổi thông số setting config của domain, ấn Manager

![Manager_Domain](https://github.com/octvitasut/fWS/blob/master/common/ajenti/manage_domain.PNG)

- Cấu hình tên Domain: Vào mục Domain nhập tên Domain và ấn Apply Configuration

![change_domain_name](https://github.com/octvitasut/fWS/blob/master/common/ajenti/website_domain_name.PNG)

- Thêm custom config cho Wordpress: Vào  tab **Advanced** 

```nginx
# This order might seem weird - this is attempted to match last if rules below fail.
location / {
    try_files $uri $uri/ /index.php?$args;
}

# Add trailing slash to */wp-admin requests.
rewrite /wp-admin$ $scheme://$host$uri/ permanent;

# Directives to send expires headers and turn off 404 error logging.
location ~* ^.+\.(ogg|ogv|svg|svgz|eot|otf|woff|mp4|ttf|rss|atom|jpg|jpeg|gif|png|ico|zip|tgz|gz|rar|bz2|doc|xls|exe|ppt|tar|mid|midi|wav|bmp|rtf)$ {
       access_log off; log_not_found off; expires max;
}

location = /favicon.ico {
    log_not_found off;
    access_log off;
}
location = /robots.txt {
    allow all;
    log_not_found off;
    access_log off;
}
# Deny all attempts to access hidden files such as .htaccess, .htpasswd, .DS_Store (Mac).
# Keep logging the requests to parse later (or to pass to firewall utilities such as fail2ban)
location ~ /\. {
    deny all;
}
# Deny access to any files with a .php extension in the uploads directory
# Works in sub-directory installs and also in multisite network
# Keep logging the requests to parse later (or to pass to firewall utilities such as fail2ban)
location ~* /(?:uploads|files)/.*\.php$ {
    deny all;
}
```

- Cấu hình PHP cho Wordpress
Click vào mục Content. Trong dropdown menu chọn PHP FastCGI rồi ấn Create
Click vào Advanced menu dưới PHP entry, thêm cấu hình sau vào **Custom configuration**

```nginx
try_files $uri =404;
fastcgi_split_path_info ^(.+\.php)(/.+)$;
```

![wordpess_php](https://github.com/octvitasut/fWS/blob/master/common/ajenti/wordpress_php.PNG)

- Cấu hình Database cho Wordpress:
Click vào mục MySQL. Tạo database và User trong cấu hình Database và User Section
![wordpess_php](https://github.com/octvitasut/fWS/blob/master/common/ajenti/wordpress_database.PNG)
## 2. Upload File 

Trong tab General, nhập url vào ô bên cạnh nút  **Download and Unpack**
```nginx
http://wordpress.org/latest.zip
```

![wordpess_php](https://github.com/octvitasut/fWS/blob/master/common/ajenti/wordpress_download.PNG)

Ấn **Download and Unpack** đợi cửa sổ terminal hiện lên và sẽ đóng lại sau khi downlaod hoàn thành

Ấn Apply config để lưu cấu hình

Ra ngoài màn hình, vào mục  **Tools**  trên sidebar, chọn  **File Manager**. Thư mục chứa domain vừa tạo nằm trong mục  `/srv/`  
Trong thư mục tồn tại thư mục  `wordpress`  được tạo khi download wordpress từ **Download and Unpack**

Copy toàn bộ nội dung trong file vào thư mục root của domain (được setting như ở trên)
 **Có thể thực hiện trên local**

## 3. Install Wordpress
 
Đi đến domain thông qua domain tạo ở trên
