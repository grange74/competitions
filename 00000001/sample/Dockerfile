# Go Benchmark Call Graph Container
#
# VERSION 0.0.1
#
# Used Keiji Yoshida's Go Benchmark Test Container (https://github.com/gobench/gobench/blob/master/Dockerfile)
# as a starting point
# 
# To build & run:
# docker build -t grange74/gobench .
# docker run --rm -v $(pwd):/profiler-output -u gobench -it grange74/gobench
# This will place a file called 'docker-callgraph.pdf' in your pwd.

FROM ubuntu:14.04
MAINTAINER Nicolas Grange <grange74@gmail.com>

# Version of Go
ENV GO_VERSION 1.4.2

# Install curl.
RUN apt-get update && apt-get install --force-yes -y curl

# Add a user.
RUN useradd -m gobench

# Install Go.
RUN curl -OL https://storage.googleapis.com/golang/go$GO_VERSION.linux-amd64.tar.gz && \
    tar -C /usr/local -xvf go$GO_VERSION.linux-amd64.tar.gz && \
    rm go$GO_VERSION.linux-amd64.tar.gz

# Install Graphviz which is needed to generate the PDF
RUN apt-get install --force-yes -y graphviz

# Setup GO paths
ENV GOPATH /home/gobench/go
ENV PATH $PATH:/usr/local/go/bin:$GOPATH/bin

# Copy over our code
ENV GOBENCH_SRC $GOPATH/src/github.com/gobench
# TODO change this to just copy over all the go and txt files instead of naming them
ADD find.go $GOBENCH_SRC/find.go
ADD find_test.go $GOBENCH_SRC/find_test.go
ADD data.txt $GOBENCH_SRC/data.txt

# change to the source directory
WORKDIR $GOBENCH_SRC

# Generate the tmp.prof 
RUN go test -c && ./gobench.test -test.cpuprofile=tmp.prof -test.bench=BenchmarkFind

# Generate the PDF of the call graph
CMD go tool pprof --pdf gobench.test tmp.prof > /profiler-output/docker-callgraph.pdf