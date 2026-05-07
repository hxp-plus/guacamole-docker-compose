FROM guacamole/guacd:1.6.0

USER root

COPY guacd/fonts/local.conf /etc/fonts/local.conf

RUN apk add --no-cache curl font-adobe-source-code-pro \
 && mkdir -p /usr/share/fonts/custom \
 && curl -fsSL -o /usr/share/fonts/custom/SourceHanSansCN-Regular.otf \
      https://raw.githubusercontent.com/dushixiang/next-terminal/27ca72d0fe951faa3b96dfe8c5185e0c3e061412/guacd/fonts/SourceHanSansCN-Regular.otf \
 && fc-cache -f

USER guacd
