
ARG PARENT_IMAGE=alpine:3.16.0
FROM $PARENT_IMAGE

ARG PKGS_TO_ADD="autoconf automake gcc git libtool make mingw-w64-gcc tar"
ARG PKGS_TO_DEL="autoconf automake gcc git libtool make mingw-w64-gcc tar"
ARG GIT_TAG="master"
ARG PKGINIT="true"
ARG PKGADD="apk add"
ARG PKGDEL="apk del"
ARG PKGCLEAN="true"

WORKDIR /work

RUN true \
    && $PKGINIT \
    && $PKGADD $PKGS_TO_ADD \
    && git clone --depth 42 --branch "${GIT_TAG:?}" https://github.com/hiddenalpha/pcre1.git . \
    && autoreconf -f -i \
    && ./configure \
         --prefix=/work/build \
         --host=x86_64-w64-mingw32 \
         --enable-pcre16 \
         --enable-pcre32 \
         --disable-cpp \
         --enable-utf \
         CC=x86_64-w64-mingw32-gcc \
         CPP=x86_64-w64-mingw32-cpp \
         CXX=x86_64-w64-mingw32-g++ \
    && make clean && make && make install \
    && tmp=$(git describe --tags 2>/dev/null | sed -e "s;^v;;") \
    && if test -n "$tmp" -a -n "$(git status --porcelain 2>/dev/null)"; then \
          tmp="${tmp}+dirty"; \
       fi \
    && if test -z "$tmp"; then tmp=$(date -u +0.0.0-%Y%m%d.%H%M%S); fi \
    && VERSION="$tmp" \
    && find build -wholename '*lib/*.la' -or -wholename '*/lib/pkgconfig/*' -or -wholename '*/bin/pcre-config' -delete \
    && (cd build && find -type f -not -name MD5SUM -exec md5sum -b {} + > MD5SUM) \
    && mkdir dist \
    && (cd build && tar --owner=0 --group=0 -f ../dist/pcre1-${VERSION:?}.tgz -c *) \
    && find /work -not -wholename '/work/dist*' -delete \
    && find /work -exec chown 1000:1000 {} + \
    && $PKGDEL $PKGS_TO_DEL \
    && $PKGCLEAN \
    && true

USER 1000:1000

CMD ["sleep", "36000"]

