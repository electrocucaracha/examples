ARG VPP_AGENT

FROM golang:alpine as build
RUN apk --no-cache add git
ENV PACKAGEPATH=github.com/networkservicemesh/networkservicemesh/
ENV GO111MODULE=on

RUN mkdir /root/networkservicemesh
ADD ["go.mod","/root/networkservicemesh"]
WORKDIR /root/networkservicemesh/
RUN go mod download

ADD [".","/root/networkservicemesh"]
RUN CGO_ENABLED=0 GOOS=linux go build -ldflags '-extldflags "-static"' -o /go/bin/vppagent-endpoint ./examples/secure-intranet/vppagent-endpoint/cmd/

FROM ${VPP_AGENT} as runtime
COPY --from=build /go/bin/vppagent-endpoint /bin/vppagent-endpoint
RUN rm /opt/vpp-agent/dev/etcd.conf; echo 'Endpoint: "0.0.0.0:9113"' > /opt/vpp-agent/dev/grpc.conf; echo "disabled: true" > /opt/vpp-agent/dev/linux-plugin.conf; echo "disabled: true" > /opt/vpp-agent/dev/telemetry.conf
COPY ./examples/secure-intranet/vppagent-endpoint/etc/ /etc/
COPY ./examples/secure-intranet/vppagent-endpoint/etc/supervisord/supervisord.conf /opt/vpp-agent/dev/supervisor.conf
