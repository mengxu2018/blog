---
title: istio mixer adapter setup
date: 2020-02-15 20:31:52
tags: istio
---

# 安装troubleshooting
https://github.com/istio/istio/wiki/Mixer-Out-Of-Process-Adapter-Walkthrough

## step1 'inconsistent vendoring' error
https://github.com/golang/go/issues/34657
解决的办法就是export GOROOT=单独的go目录，不要go下面的src有istio的那个
export GOROOT=/root/software/go

## Step 2 BUILD_WITH_CONTAINER=1 make gen
Just to ensure everything is good, let's generate the file and build the code. Go to the top of the repo and do:
`BUILD_WITH_CONTAINER=1 make gen`
实际上是
```
export BUILD_WITH_CONTAINER=1
cd /root/kube/go/src/istio.io/istio
make gen
```
不过这个里面有个证书的错误，暂时没有影响，可以再观察

## step3 mygrpcadapter
mygrpcadapter.go:40:2: no matching versions for query "latest"

"istio.io/istio/pkg/log" -> "istio.io/pkg/log"

## step 5 hang on
第五步中的 pushd $ISTIO/istio && make mixs貌似需要minikube服务是shutdown的时候才可以安装成功，
但是docker daemon要启动

mixc，mixs都在/root/kube/go/src/istio.io/istio/out/linux_amd64/下面，
也就是说在$ISTIO/istio/out/linux_amd64下面，
但是文档里面说的是在$GOPATH/out/linux_amd64/release/

## go build
When compiling multiple packages or a single non-main package, build compiles the packages but discards the resulting object, serving only as a check that the packages can be built.

go build编译main package的会生成执行文件，非main的就没有输出，just 验证下


# 总结
按照这个文章弄好的其实还是需要打包部署成一个kubenetes的service， 不可能每次都是cmd下面的脚本运行adapter，
其实就是把cmd下面的go文件编译一下，部署成kubernetes里面的service。

mygrpcadapter/config下面的.proto文件其实是为了生成mygrpcadapter.yaml，这个文件也是kubernetes的一个资源，需要kubectl apply
至于config/config.proto里面的Params是给sample_operator_cfg.yaml里面的kind: handler使用的，
sample_operator_cfg.yaml里面的instance用了metric template，会封装数据参数传给adapter。
The operator that configures Istio controls how this template-specific data is constructed and dispatched to adapters.
https://github.com/istio/istio/wiki/Mixer-Out-Of-Process-Adapter-Dev-Guide


adapter的rule也会定义规则，比如只允许特定的host访问，
到时候浏览器访问某个service的时候，首先gateway会转发到特定的service，这个是gateway和virtualservice来做的，
adapter这边的rule是另外一码事
