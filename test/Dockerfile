FROM registry.gitlab.com/formations-kgaut/template-reveal-js:latest

RUN apk update && \
    apk add nginx

COPY nginx.conf /etc/nginx/http.d/default.conf

EXPOSE 80

STOPSIGNAL SIGQUIT

CMD ["nginx", "-g", "daemon off;"]