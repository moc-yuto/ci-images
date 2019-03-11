FROM alpine:3.8 AS builder
RUN apk add --update --no-cache --virtual .build-deps build-base git \
      && git clone https://github.com/google/jsonnet.git \
      && cd jsonnet \
      && make libjsonnet.so \
      && mkdir -p /opt/jsonnet \
      && cp libjsonnet.so /opt/jsonnet/libjsonnet.so \
      && cp include/libjsonnet.h /opt/jsonnet/libjsonnet.h \
      && cd .. \
      && rm -rf jsonnet \
      && apk del --purge .build-deps

FROM ruby:2.5-alpine3.8
COPY --from=builder /opt/jsonnet/libjsonnet.so /usr/local/lib/libjsonnet.so
COPY --from=builder /opt/jsonnet/libjsonnet.h /usr/local/include/libjsonnet.h

ENV HAKO_COMMIT_HASH=2e5f9ee6f10411cd7ec9e84e0c8bf038231c9a7f

RUN apk --update --no-cache add openssh git zip make docker libstdc++ \
      && apk add --update --no-cache --virtual .build-deps g++ \
      && gem install specific_install -N \
      && JSONNET_USE_SYSTEM_LIBRARIES=1 gem specific_install https://github.com/eagletmt/hako.git --ref ${HAKO_COMMIT_HASH} \
      && gem specific_install https://github.com/moaikids/hako-parameterstore.git -r 8ed6452bd15e24c01b0143de6bccb5bfa86942a4 \
      && apk del --purge .build-deps
