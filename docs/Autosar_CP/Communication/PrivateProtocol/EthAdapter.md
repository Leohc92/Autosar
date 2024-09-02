
```c
typedef struct TLVInfo{
  uint8_t msg_c;    //use channel (number)
  uint8_t data_flag;
  uint16_t msg_tid; //索引channel id (index)
  Msgp_t msg_p;     //properity 1
  uint16_t msg_s;   //sequence
  uint16_t msg_l;   //payload length
  uint16_t msg_setdlen;   //usr setD length
  uint8_t* msg_vx;   //mesage storage address
  uint8_t* msg_loc; //dynamic loc 
  uint64_t msg_ts;  //timestamp
  uint64_t msg_localSnCnt;  //mutiple package ,when Sn is active,Cnt++ 
} TLVInfo_t;
```


```c
typedef struct LogInfo
{
  uint8_t is_failed;
  uint8_t msg_channel;
  uint16_t failed_total_num;
  uint16_t msg_total_num;
  uint16_t msg_tid;
  uint32_t error_type;
}LogInfo_t;
```


**Proc_RegisterTLVTx**
TLVLoc_u16_t  索引

