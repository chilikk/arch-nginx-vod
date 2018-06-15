pkgname=nginx-vod
_pkgname=nginx
pkgver=1.15.0
_vodver=1.23
_davextver=0.1.0
pkgrel=1
pkgdesc='Lightweight HTTP server and IMAP/POP3 proxy server, with nginx-vod-module'
arch=('i686' 'x86_64')
url='https://nginx.org'
license=('custom')
depends=('pcre' 'zlib' 'openssl' 'geoip' 'mailcap')
provides=('nginx')
conflicts=('nginx')
backup=('etc/nginx/fastcgi.conf'
        'etc/nginx/fastcgi_params'
        'etc/nginx/koi-win'
        'etc/nginx/koi-utf'
        'etc/nginx/nginx.conf'
        'etc/nginx/scgi_params'
        'etc/nginx/uwsgi_params'
        'etc/nginx/win-utf'
        'etc/logrotate.d/nginx')
install=nginx.install
source=($url/download/nginx-$pkgver.tar.gz
        https://github.com/kaltura/nginx-vod-module/archive/$_vodver.tar.gz
        https://github.com/arut/nginx-dav-ext-module/archive/v$_davextver.tar.gz
        service
        logrotate)
sha512sums=('7dbdf437d8d546059a8a03aa9c8d2be98dba7306e2daa49611c16f1e56413a25d4c622da13a815e8075a10f4a0cd744167deaeb971c0a69189940a7a05fa32df'
         '9ab8a051ac1d68e32f2b6bddc9985adb5580d3d9af9b6c2ba83cf0348b1d2b231f113f0237375d08dae99c884e7f0f5529ac4a0ad47c1956aacab3b0bf66fb0a'
         '47b1686b483640a7fdcbf8081aae2e9f83fb0072ef0940b1cd7f8ddf4932317740b38f0dd4a8f3dd8da074c11c70038ac6758c0feafd3851331acdc85f3e0ee1'
         '4f90db6b8b5c13762b96ddff9ca4e846762d46b90be27c7c9d54cec6f7f12fc95585f8455919296edb0255405dd80af8ee86780b805631b72eb74ee59f359715'
         '9232342c0914575ce438c5a8ee7e1c25b0befb457a2934e9cb77d1fe9a103634ea403b57bc0ef0cd6cf72248aee5e5584282cea611bc79198aeac9a65d8df5d7')

_common_flags=(
  --with-compat
  --with-file-aio
  --with-http_addition_module
  --with-http_auth_request_module
  --with-http_dav_module
  --with-http_degradation_module
  --with-http_flv_module
  --with-http_geoip_module
  --with-http_gunzip_module
  --with-http_gzip_static_module
  --with-http_mp4_module
  --with-http_realip_module
  --with-http_secure_link_module
  --with-http_slice_module
  --with-http_ssl_module
  --with-http_stub_status_module
  --with-http_sub_module
  --with-http_v2_module
  --with-mail
  --with-mail_ssl_module
  --with-pcre-jit
  --with-stream
  --with-stream_geoip_module
  --with-stream_realip_module
  --with-stream_ssl_module
  --with-stream_ssl_preread_module
  --with-threads
)

_stable_flags=(
)

build() {
  sed -i 's/CODEC_CAP_VARIABLE_FRAME_SIZE/AV_CODEC_CAP_VARIABLE_FRAME_SIZE/' nginx-vod-module-$_vodver/vod/filters/audio_encoder.c
  sed -i 's/CODEC_FLAG_GLOBAL_HEADER/AV_CODEC_FLAG_GLOBAL_HEADER/' nginx-vod-module-$_vodver/vod/filters/audio_encoder.c

  cd $_pkgname-$pkgver

  ./configure \
    --prefix=/etc/nginx \
    --conf-path=/etc/nginx/nginx.conf \
    --sbin-path=/usr/bin/nginx \
    --pid-path=/run/nginx.pid \
    --lock-path=/run/lock/nginx.lock \
    --user=http \
    --group=http \
    --http-log-path=/var/log/nginx/access.log \
    --error-log-path=stderr \
    --http-client-body-temp-path=/var/lib/nginx/client-body \
    --http-proxy-temp-path=/var/lib/nginx/proxy \
    --http-fastcgi-temp-path=/var/lib/nginx/fastcgi \
    --http-scgi-temp-path=/var/lib/nginx/scgi \
    --http-uwsgi-temp-path=/var/lib/nginx/uwsgi \
    --with-cc-opt="-O3" \
    --add-module=$srcdir/nginx-vod-module-$_vodver \
    --add-module=$srcdir/nginx-dav-ext-module-$_davextver \
    ${_common_flags[@]} \
    ${_stable_flags[@]}


  make
}

package() {
  cd $_pkgname-$pkgver
  make DESTDIR="$pkgdir" install

  sed -e 's|\<user\s\+\w\+;|user html;|g' \
    -e '44s|html|/usr/share/nginx/html|' \
    -e '54s|html|/usr/share/nginx/html|' \
    -i "$pkgdir"/etc/nginx/nginx.conf

  rm "$pkgdir"/etc/nginx/*.default
  rm "$pkgdir"/etc/nginx/mime.types  # in mailcap

  install -d "$pkgdir"/var/lib/nginx
  install -dm700 "$pkgdir"/var/lib/nginx/proxy

  chmod 755 "$pkgdir"/var/log/nginx
  chown root:root "$pkgdir"/var/log/nginx

  install -d "$pkgdir"/usr/share/nginx
  mv "$pkgdir"/etc/nginx/html/ "$pkgdir"/usr/share/nginx

  install -Dm644 ../logrotate "$pkgdir"/etc/logrotate.d/nginx
  install -Dm644 ../service "$pkgdir"/usr/lib/systemd/system/nginx.service
  install -Dm644 LICENSE "$pkgdir"/usr/share/licenses/$_pkgname/LICENSE
  install -Dm644 ../nginx-vod-module-$_vodver/LICENSE "$pkgdir"/usr/share/licenses/nginx-vod-module/LICENSE

  rmdir "$pkgdir"/run

  install -d "$pkgdir"/usr/share/man/man8/
  gzip -9c man/nginx.8 > "$pkgdir"/usr/share/man/man8/nginx.8.gz

  for i in ftdetect indent syntax; do
    install -Dm644 contrib/vim/$i/nginx.vim \
      "$pkgdir/usr/share/vim/vimfiles/$i/nginx.vim"
  done
}

# vim:set ts=2 sw=2 et:
