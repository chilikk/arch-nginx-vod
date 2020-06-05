pkgname=nginx-vod
_pkgname=nginx
pkgver=1.19.0
_vodver=1.26
_davextver=3.0.0
pkgrel=1
pkgdesc='Lightweight HTTP server and IMAP/POP3 proxy server, with nginx-vod-module'
arch=('i686' 'x86_64')
url='https://nginx.org'
license=('custom')
depends=('libxml2' 'libxslt' 'pcre' 'zlib' 'openssl' 'geoip' 'mailcap')
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
sha512sums=('3240d5dc59877f9d6a95c8779240675cec9290df079b9d52c06147e58900f2e060e768729669ffaf9a2a90bb9abbe8ab7fba24ff65d45fec9eeb3b6733b65f30'
         '2e8fa53d19fa415c6ef6934ab4f5cb846323dc34dfdc7d352cda4da66d8082edf513d646bc51e7b058aee8895768a0fdebf152e7583d80cde39fc5d80fa338d5'
         'd0193ba90f1ef46c4e470630c4394bdf99d94fd2e3bd8be6cb2ba1655ec59944b1269025f032b79dc2c6dad366e54389ef6a6da2ddeb91d535a4027f2162fbde'
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
