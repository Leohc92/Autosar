PduR_RmSrcRom ——



- **PduR_Bm_CopyDataAsPtr(bmTxBufferInstanceRomIdx, &info, actualPduLengthToBeRouted, memIdx);**  
	- bmTxBufferInstanceRomIdx (TxBuffer ID)
	- info 输出info
	- actualPduLengthToBeRouted  请求数据的长度 - buffer包含数据的长度
	- memIdx   (memory section index) rmSrcIdx ->
	```C
	PduR_RmBufferedTpPropertiesRomPartitionIdxOfRmSrcRomType memIdx = PduR_GetRmBufferedTpPropertiesRomPartitionIdxOfRmSrcRom(rmSrcIdx);
	```



RxIndication中获取接收到地数据


```c
/**********************************************************************************************************************
 *  DRT_PduR_TpRxIndication()
 *********************************************************************************************************************/
/*! \brief      Function called by PduR to inform the DRT about a received TP PDU.
 * \details     
 * \param[in]   id      The PduR ID of the received PDU.
 * \param[in]   result  Result of the reception.
 * \pre         -
 * \context     TASK|ISR1|ISR2
 * \reentrant   TRUE for id which does not belong to the same configured port.
 * \synchronous TRUE
 * \config      
 */
void DRT_PduR_TpRxIndication(PduIdType id, Std_ReturnType result)
{

#ifdef DIAGROUTING_TEST_MCUA
    for ( uint8 i = 0u; i < PDUR_Target_MCUA_Num; i++)
    {
        if ( id == DRT_PDUR_RoutingTable_MCUA[i] )
        {
            if (PDUR_Counter_RxIndication_MCUA[i]==0xFFFFFFFFu)
                PDUR_Counter_RxIndication_MCUA[i] = 0;
            else
                PDUR_Counter_RxIndication_MCUA[i]++;
        }
    }
#else
    for ( uint8 i = 0u; i < PDUR_Target_MCUB_Num; i++)
    {
        if ( id == DRT_PDUR_RoutingTable_MCUB[i] )
        {
            if (PDUR_Counter_RxIndication_MCUB[i]==0xFFFFFFFFu)
                PDUR_Counter_RxIndication_MCUB[i] = 0;
            else
                PDUR_Counter_RxIndication_MCUB[i]++;
        }
    }

    result = result;
#endif
    PduR_BmTxBufferInstanceRomIterType bmTxBufferInstanceRomIdx;
    PduInfoType info;
    PduR_BmTxBufferRomIterType bmTxBufferRomIdx;
    PduR_RmSrcRomIdxOfRxTp2DestType rmSrcIdx = PduR_GetRmSrcRomIdxOfRxTp2Dest(id);
    PduR_RmBufferedTpPropertiesRomPartitionIdxOfRmSrcRomType memIdx = PduR_GetRmBufferedTpPropertiesRomPartitionIdxOfRmSrcRom(rmSrcIdx);
    PduR_FmFifoElementRamIterType fmFifoElementRamWriteIdx;

    if(PduR_RmTp_GetCurrentlyWrittenFmFifoElementRamIdxByRmSrcIdx(rmSrcIdx, &fmFifoElementRamWriteIdx) == E_OK) /* SBSW_PDUR_STACK_VARIABLE */ /* COV_PDUR_ROBUSTNESS */
    {
        PduR_Fm_GetCurrentlyUsedBmTxBufferRomIdxByFmFifoElementIdx(fmFifoElementRamWriteIdx, bmTxBufferRomIdx, memIdx); /* SBSW_PDUR_VALID_PTR_ENSURED_BY_CALLING_FUNCTION */
    }
    bmTxBufferInstanceRomIdx = PduR_GetBmTxBufferInstanceRomStartIdxOfBmTxBufferRom(bmTxBufferRomIdx, memIdx);
    PduLengthType actualPduLengthToBeRouted = PduR_Bm_GetActualPduLengthToBeRouted(bmTxBufferInstanceRomIdx, memIdx);

    /* Get Data Pointer for Transmit */
    PduR_Bm_CopyDataAsPtr(bmTxBufferInstanceRomIdx, &info, actualPduLengthToBeRouted, memIdx);       /* SBSW_PDUR_API_CALL_FORWARDING_ID_AND_PTR */

    info = info;
    result = result;

    /* rmSrcIdx */
    


    /* Judge */
    if (( info.SduDataPtr[0] == 0x02 ) && ( info.SduDataPtr[1] == 0x3E))
    {
        
    }

}
```



#### Buffer机制

> [!Note] Buffer机制
> EcuC structural changes with PduR version 15.03


每个PduR RoutingPath需要配置PdurDestPdu配置一个Queue，每个Queue可以有多种实现方式：
##### 1. Single Buffer Queue
只能用于IF接口的数据触发式发送，没有Queue的配置参数，其实就是Queue Depth默认为0，也就是Last-is-Best策略
##### 2. Communication Interface Queue
只能用于IF接口，有着唯一的参数Queue Depth可配置，就是带有FIFO的Queue，对于IF接口数据传输，非常建议使用这种
##### 3. Shared Memory Queue
可适用于IF接口与TP接口，带有FIFO的Buffer，可配置的参数包括：
**PduRQueueDepth**
**PduRTpThreshold**
只有TP接口
**PduRExplicitTxBufferRef**
显性Tx Buffer
**PduRImplicitTxBufferRef**
自动配置
**PduRLockRef**
自动配置

对于每种Queue都需要配置：
1. PduR Queue Depth 
2. PduR Tp Threshold
3. PduR Explicit TxBufferRef

![](../../pics/Pasted%20image%2020240806112430.png)
##### 相关代码
```c
/**   \brief  type used in PduR_FmFifoInstanceRam */
typedef struct sPduR_FmFifoInstanceRamType
{
  PduR_BmTxBufferInstanceRomIdxOfFmFifoInstanceRamType BmTxBufferInstanceRomIdxOfFmFifoInstanceRam;  /**< the index of the 0:1 relation pointing to PduR_BmTxBufferInstanceRom */
} PduR_FmFifoInstanceRamType;

/**   \brief  type to be used as symbolic data element access to PduR_FmFifoElementRam in the partition context CommonSharedMemory */
typedef struct PduR_FmFifoElementRamCommonSharedMemoryStructSTag
{
  PduR_FmFifoElementRamType PduRQueue_DCM_MCUAToVgmPhyResDiag[4];
  PduR_FmFifoElementRamType PduRQueue_DCM_VgmToMCUAPhyReqDiag[4];
  PduR_FmFifoElementRamType PduRQueue_DoCan_Router_AdcuToAdpuDiag[4];
  PduR_FmFifoElementRamType PduRQueue_DoCan_Router_AdcuToDvrDiag[4];
  PduR_FmFifoElementRamType PduRQueue_DoCan_Router_AdcuToPdcmDiag[4];
  PduR_FmFifoElementRamType PduRQueue_DoCan_Router_AdcuToRmlDiag[4];
  PduR_FmFifoElementRamType PduRQueue_DoCan_Router_AdpuToAdcu[4];
  PduR_FmFifoElementRamType PduRQueue_DoCan_Router_DvrToAdcu[4];
  PduR_FmFifoElementRamType PduRQueue_DoCan_Router_PdcmToAdcu[4];
  PduR_FmFifoElementRamType PduRQueue_DoCan_Router_RmlToAdcu[4];
  PduR_FmFifoElementRamType PduRQueue_DoIpInt_Router_VGM_TO_FLC_B[4];
  PduR_FmFifoElementRamType PduRQueue_VgmToAllFuncDiag[8];
  PduR_FmFifoElementRamType PduRQueue_DoIpInt_Router_VGM_TO_FLC_A[4];
  PduR_FmFifoElementRamType PduRQueue_DoIpInt_Router_VGM_TO_FLR3_A[4];
  PduR_FmFifoElementRamType PduRQueue_DoIpInt_Router_VGM_TO_FSRL_A[4];
  PduR_FmFifoElementRamType PduRQueue_DoIpInt_Router_VGM_TO_FSRR_A[4];
  PduR_FmFifoElementRamType PduRQueue_DoIpInt_Router_VGM_TO_SODL_A[4];
  PduR_FmFifoElementRamType PduRQueue_DoIpInt_Router_VGM_TO_SODR_A[4];
  PduR_FmFifoElementRamType PduRQueue_DoIpInt_Router_VGM_TO_FLR3_B[4];
  PduR_FmFifoElementRamType PduRQueue_DoIpInt_Router_VGM_TO_FSRL_B[4];
  PduR_FmFifoElementRamType PduRQueue_DoIpInt_Router_VGM_TO_FSRR_B[4];
  PduR_FmFifoElementRamType PduRQueue_DoIpInt_Router_VGM_TO_SODL_B[4];
  PduR_FmFifoElementRamType PduRQueue_DoIpInt_Router_VGM_TO_SODR_B[4];
  PduR_FmFifoElementRamType PduRQueue_DoCan_Router_AdcuToIrfmDiag[4];
  PduR_FmFifoElementRamType PduRQueue_DoCan_Router_AdcuToIrrmDiag[4];
  PduR_FmFifoElementRamType PduRQueue_DoCan_Router_IrfmToAdcu[4];
  PduR_FmFifoElementRamType PduRQueue_DoCan_Router_IrrmToAdcu[4];
} PduR_FmFifoElementRamCommonSharedMemoryStructSType;
```


这里的FmFifoElementRamStartIdx - FmFifoElementRamEndIdx 应该就是各个Queue对应的Queue Depth = 4
![](../../pics/Pasted%20image%2020240808170943.png)
```c
```


