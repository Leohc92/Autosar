顾名思义， PduR是对Pdu的路由，PduR支持IF与TP总线的

#### Interface类型
##### 1. Communication Interface (IF接口)
##### 2. Transport Protocol （TP接口）


| 接口类型 | 上层         | 下层                              |
| ---- | ---------- | ------------------------------- |
| IF   | Com, IPduM | LinIf, CanIf, CanNm, FrIf, FrNm |
| TP   | Com, Dcm   | J1939Tp, LinTp, CanTp, FrTp     |

PduR模块中会有一个或多个Routing table（路由表），以及PDU路由引擎

![](../../pics/Pasted%20image%2020240807161610.png)

PduR功能主要是
1.  I-PDU Forwarding
2.  I-PDU Gatewaying
**on-the-fly gatewaying**是适用于TP gateway中，当I-PDU未接收完全时，已经开始转发



#### 功能描述
