## 运行环境

```
CPU: Intel(R) Xeon(R) CPU X3320  @ 2.50GHz  4核
内存：8GB
网卡：千兆网卡
Client与Server之间PING值：0.854ms

```

## 测试程序

- echo服务<br/>server将请求字符串直接返回
- 延时echo服务<br/>server先sleep 100ms, 再将请求字符串返回
- rpc-echo服务<br/>server将请求转发至server2, server2将请求字符串直接返回
- 延时rpc-echo服务<br/>server将请求转发至server2， server2先sleep 100ms, 再将请求字符串返回

备注：请求字符串小于100字节

## TCP请求连接模式

- 短连接<br/>每个请求都需要建立TCP连接
- 长连接<br/>建立TCP连接后，保持不断开，连续发送请求

## 程序A测试结果(QPS及延时分布)

| | QPS | CPU | <5ms | 5 ~ 10ms | 10 ~ 30ms| 30 ~ 50ms | >50ms |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 长连接 | 85,000/s | 98% | 99.25% | 0.66% | 0.09% | 0 | 0 |
| 短连接 | 30,000/s | 70% | 94.50% | 5.30% | 0.20% | 0 | 0 |

备注：短连接并没有压满CPU，因为proxy为单进程，成为瓶颈。

## 程序B测试结果QPS

| | QPS | CPU | <120ms | 120 ~ 150ms | 150 ~ 200ms | > 200ms |
| --- | --- | --- | --- | --- | --- | --- |
| 长连接 | 55,000/s | 89% | 94.71% | 4.83% | 0.25% | 0.21% |
| 短连接 | 25,000/s | 70% | 93.12% | 3.11% | 1.72% | 2.05% |

备注：CPU并没有压满，继续往上压，时延明显变大。

## 程序C测试结果QPS

| | QPS | CPU | <5ms | 5 ~ 10ms | 10 ~ 30ms| 30 ~ 50ms | >50ms |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 长连接 | 45,000/s | 88% | 26.67% | 72.06% | 1.27% | 0.002% | 0.015% | 
| 短连接 | 30,000/s | 70% | 52.33% | 46.81% | 0.84% | 0.02% | 0 |

备注：都没有压满CPU，压满CPU后时延明显变大。

## 程序D测试结果QPS

| | QPS | CPU | <120ms | 120 ~ 150ms | 150 ~ 200ms | > 200ms |
| --- | --- | --- | --- | --- | --- | --- |
| 长连接 | 45,000/s | 92% | 48.77% | 47.54% | 3.30% | 0.39% |
| 短连接 | 25,000/s | 78% | 95.40% | 0.85% | 0 | 3.76% |

备注：短连接proxy成为瓶颈。
