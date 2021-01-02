FROM python:alpine

WORKDIR /app
EXPOSE 2021

RUN apk add --update --no-cache dumb-init \
  && rm -rf /var/cache/apk/*

COPY server.py .

RUN adduser -D runner

USER runner
ENTRYPOINT ["/usr/bin/dumb-init", "--"]
CMD ["python", "server.py"]
