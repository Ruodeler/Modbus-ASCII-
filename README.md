# Modbus-ASCII-
MODBUS ASCII lrc8
#include "stm32f10x.h"

  
u8 Rcv_Buffer[131]={0};			  
u8 Send_Buffer[131]={0};
u16 Rcv_Index=0;
u16 Send_Index=0;
u8 Comu_Busy=0;
u8	Sendcount=0;	
u8	Recivecount=0;
u16 KAIGUANLIANG=0;
extern u16 DLBJ,dlBJ,DWBJ,BJBZ,BJBZ1,BJBZ2,BJBZ3,BJBZ4,BJBZ5;         //¶ÏÂ·±¨¾¯    

extern u16 DL1,DL2,DL3,DL4,DL5,DL6,DL7,DL8,DL9,DL10,DL11,DL12;
u16 DL=0;
u16 SendFlag=0;
extern u16 Rcv_For_User[80];//Õâ¸öÊý×éÊÇÒ»¸ö¶ÔÍâ½Ó¿Ú£¬Ö÷³ÌÐòÖÐ´ÓÕâ¸öÊý×éÖÐÈ¡³ö¿ÉÓÃµÄÊý¾Ý¡£µÚÒ»¸öÔªËØÖ»ÓÐ0ºÍ1Á½¸ö¿ÉÄÜÖµ£¬ÓÃÓÚ±íÊ¾Êý×éÄÚÈÝÊÇ·ñÒÑ¾­¸úÐÂ¹ý¡£
extern u16 Send_For_User[80];//Õâ¸öÊý×éÊÇÒ»¸ö¶ÔÍâ½Ó¿Ú£¬Ö÷³ÌÐò½«Òª·¢ËÍµÄÊý¾Ý·ÅÖÃÔÚÕâ¸öÊý×éÖÐ¡£µÚÒ»¸öÔªËØÖ»ÓÐ0ºÍ1Á½¸ö¿ÉÄÜÖµ£¬ÓÃÓÚ±íÊ¾Êý×éÄÚÈÝÊÇ·ñÒÑ¾­¸úÐÂ¹ý¡£
extern u16 XH1;
extern u16 I1_1,IC_1,LIM_1,D_1,C_1;    //ÄÇ¼¸Â·µÄÄ£ÄâÖµ
extern u16 I1_2,IC_2,LIM_2,D_2,C_2;
extern u16 I1_3,IC_3,LIM_3,D_3,C_3;
extern u16 I1_4,IC_4,LIM_4,D_4,C_4; 
extern u16 I1_5,IC_5,LIM_5,D_5,C_5; 
extern u16 I1_6,IC_6,LIM_6,D_6,C_6; 
extern u16 I1_7,IC_7,LIM_7,D_7,C_7; 
extern u16 I1_8,IC_8,LIM_8,D_8,C_8; 
extern u8 LINEAR1,LINEAR2,LINEAR3,LINEAR4;
extern u16 YALI,XIEHE,ZUOYI;//Ñ¹Á¦¿ª¹Ø£¬Ð¶ºÉ¿ª¹Ø£¬×ùÒÎ¿ª¹Ø
extern u16 T1_1,T1_2,T2_1,T2_2,T3_1,T3_2,T4_1,T4_2;  
extern u32 I_FEEDBACK1,I_FEEDBACK2,I_FEEDBACK3,I_FEEDBACK4,I_FEEDBACK5,I_FEEDBACK6,I_FEEDBACK7,I_FEEDBACK8;
extern u32 I_FEEDBACK9,I_FEEDBACK10,I_FEEDBACK11,I_FEEDBACK12;		
extern u32 V_DONGBI, V_ZONG, V_DONGBI,V_FANDOU,V_FUZHU1,V_FUZHU2;
////#define YX1 GPIO_ReadInputDataBit(GPIOA, GPIO_Pin_11);
//#define YX2 GPIO_ReadInputDataBit(GPIOC, GPIO_Pin_11);
//#define YX3 GPIO_ReadInputDataBit(GPIOC, GPIO_Pin_12);
//#define YX4 GPIO_ReadInputDataBit(GPIOD, GPIO_Pin_0);
//#define YL  GPIO_ReadInputDataBit(GPIOD, GPIO_Pin_4);
//#define ZW  GPIO_ReadInputDataBit(GPIOD, GPIO_Pin_3);
//#define XH  GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_7);
extern u8 YX1,YX2,YX3,YX4,YL,ZW,XH;
void MODBUS_Rcv(void);  //£¨½ÓÊÜ£©
void MODBUS_Send(void); //£¨·¢ËÍ£©
void LRC_Cal(u8 *buffer1,u8 len,u8 *buffer2);//LRCÐ£ÑéÂë
void response_handle(void);                  //µ±½ÓÊÜÍêÖ÷»úµÄÐÅÏ¢ºó£¬½øÐÐ´¦Àí
void Respond_Read(void);//ÉèÖÃÄ£Ê½ÏÂ£¬ÎÒµÄ»ØÓ¦
void Respond_Write(void);                         //¼à¿ØÉèÖÃÄ£Ê½ÏÂ£¬ÎÒµÄ»ØÓ¦
void Sendvalue_Init(void);                       //·¢ËÍÖµµÄ³õÊ¼»¯
void HexToAscii(u8 *val);                    //Ê®Áù½øÖÆ×ªÎªASCIIÂë
u8 AsciiToHex(u8 val);                       //ASCIIÂë×ª»»Îª16½øÖÆ 
extern u16 DBJ(u16 n);                             //¶ÏÂ·±¨¾¯¼ì²â
extern u16 dBJ(u16 n);                             //¶ÌÂ·±¨¾¯¼ì²â
extern u16 DDBJ(u16 n);                            //µçÎ»Æ÷¶ÏÂ·±¨¾¯¼ì²â¡£ 
extern u16 XBJ(u16 n);                             //ÏµÍ³±¨¾¯¹ýÑ¹¼ì²â 
extern u16 XBJ1(u16 n);                            //ÏµÍ³±¨¾¯Ç·Ñ¹¼ì²â
extern u16 get_temperature(void);	          //ÎÂ¶È¼ì²â 
 /* º¯Êý¹¦ÄÜ£ºÓÃÓÚMODBUSeÐÅÏ¢½ÓÊÕ
 	º¯ÊýÊäÈë£ºÎÞ¡£														    
	º¯ÊýÊä³ö£ºÎÞ¡£                                                                                      */
  void MODBUS_Rcv(void)
  {
  	u8 Rcv_Data;
	if(Comu_Busy==0)//Èç¹û±¾»ú²»Ã¦£¬¿ÉÒÔ½ÓÊÕÐÂµÄÖ¡
	{
		Rcv_Data = USART_ReceiveData(USART3);                  //´ó¸ç£¬ÎÒÃÇÄÇ¶ùÊÇ3
		if(Rcv_Data == ':')//Ö¡ÆðÊ¼±êÖ¾,±êÖ¾ÐÂµÄÖ¡¿ªÊ¼
		{
			Rcv_Index = 0;//½«Ö¡¶¨Î»µ½Rcv_Buffer´æ·ÅµÚÒ»¸öÓÐÐ§×Ö½ÚµÄÎ»//ÓÐ¿ÉÄÜÒª¸ÄÎª0£¨Ã²ËÆÓÖ²»ÓÃ¸ÄÁË£©£¨ÆäÊµ»¹ÊÇ¸ÄÎª0ÁË£©
			Rcv_Buffer[Rcv_Index] = ':';
			Rcv_Index++;//ÒÆµ½ÏÂÒ»¸öÎ»ÖÃ
		  Recivecount++;//½ÓÊÕµÄÓÐÐ§×Ö½ÚÊý¼ÓÒ»
		}
		else if(Rcv_Data == '\n')//Èç¹ûÊÇ0x13£¬×îºóÒ»¸öÊý      //Ô­Îª'\r'
		{
			Rcv_Buffer[Rcv_Index] = Rcv_Data;//´æ·Å½ÓÊÕµ½µÄ×Ö½Ú
			Recivecount++;//½ÓÊÕµÄÓÐÐ§×Ö½ÚÊý¼ÓÒ»
			if(Rcv_Index>=1&&Rcv_Buffer[Rcv_Index-1]=='\r')//±êÖ¾Ö¡½áÊø Ô­Îª'*'
			{
				Comu_Busy=1;//¿ªÊ¼´¦Àí»Ø¸´µÄÖ¡ÐÅÏ¢£¬½øÈëÃ¦×´Ì¬
				response_handle();		//´¦ÀíÊý¾Ý
				Recivecount=0;//Êý×éÇåÁã
				Comu_Busy=0;//»Ö¸´¿ÕÏÐ×´Ì¬
			}		 
		}
		else//Èç¹ûÊÇ±ðµÄ×Ö·û(Èç¹û½ÓÊÕµ½µÄÊÇÒ»¸öÊý×é)
		{
			Rcv_Buffer[Rcv_Index] = Rcv_Data;//´æ·Å½ÓÊÕµ½µÄ×Ö½Ú
			Rcv_Index++;//ÒÆµ½ÏÂÒ»¸öÎ»ÖÃ
			Recivecount++;//½ÓÊÕµÄÓÐÐ§×Ö½ÚÊý¼ÓÒ»					
		}	
	}		
  }

 /* º¯Êý¹¦ÄÜ£ºÓÃÓÚModbusÐÅÏ¢·¢ËÍ
 	º¯ÊýÊäÈë£ºÎÞ¡£														    
	º¯ÊýÊä³ö£ºÎÞ¡£																	   */
  void MODBUS_Send(void)	
 {
		u8 Send_Data;
		Send_Data = Send_Buffer[Send_Index];	           //
  	USART_SendData(USART3,Send_Data);                //ÒòÎªÎÒÃÇÓÃµÄÊÇ3
		Send_Index++;		       
	if(Send_Index>=Sendcount)//Èç¹û·¢ËÍÍêÖ¸¶¨µÄÊý¾ÝÒÔºó
	{
		USART_ITConfig(USART3, USART_IT_TC, DISABLE);//¹Ø±ÕÊý¾Ý·¢ËÍÖÐ¶Ï
		Send_For_User[0]=0;//±íÊ¾Êý¾ÝÒÑ¾­Ê¹ÓÃÍê£¬´ý¸úÐÂ
	}
			
 }


 /* º¯Êý¹¦ÄÜ£ºASCII¼ÆËãÓÃº¯Êý
 	º¯ÊýÊäÈë£ºbuffer1ÊÇÒª½øÐÐLRC¼ÆËãµÄÐÅÏ¢Ê×µØÖ·£¬lenÊÇÐÅÏ¢ÖÐ×Ö½ÚÊý£¬buffer2ÊÇ½ÓÊÕLRCÐ£Ñé½á¹ûµÄ´æ´¢¿Õ¼äÊ×µØÖ·														    
	º¯ÊýÊä³ö£ºÎÞ                                                                                      */
 

void LRC_Cal(u8 * buffer1, u8 len, u8 * buffer2)    //??????16???ASCII?
{
	u8 i = 0;
u8 temp = 0;
u8 buffer[80] = { 0 };

   while (i <(len/2))
	{
		buffer[i] = AsciiToHex(*(buffer1 + 2 * i))*16  +( AsciiToHex(*(buffer1 + 2 * i + 1)));
		temp += buffer[i];
		i++;
	}
	
	temp = temp % 0X100;
	temp =255-temp;
	temp= temp + 1;
	*buffer2 = temp/16;
	*(buffer2 + 1) = (temp % 16);
	HexToAscii(buffer2);
	HexToAscii(buffer2 + 1);

	
}



void HexToAscii(u8 *val)   //Ê®Áù½øÖÆ×ª»»ÎªASCIIÂë
{
	if(*val>9)
		*val += 'A'-10;//×ª»»³É×ÖÄ¸µÄasciiÂë
	else
		*val += '0';//×ª»»³ÉÊý×ÖµÄasciiÂë
	return;
}


u8 AsciiToHex(u8 val)      //ASCII×ª»¯Îª16½øÖÆ
{
	if(val>='0'&&val<='9')   
	{
	 	val -= '0';
		return val;
	}
	if(val>='A'&&val<='F')
	{
		val -= 'A'-10;
		return val;
	}
	return val;
}
 /* º¯Êý¹¦ÄÜ£º¶Ô½ÓÊÕµÄ»Ø¸´ÐÅÏ¢½øÐÐ´¦Àí,´Ó»Ø¸´ÐÅÏ¢»ñµÃÐèÒªµÄÊý¾Ý
 	º¯ÊýÊäÈë£ºÎÞ¡£														    
	º¯ÊýÊä³ö£ºÎÞ¡£                                                                                      */
void response_handle(void)	//»Ø¸´µÄÊÇRDµÄÃüÁî£¬WDµÄÃüÁîÃ»ÓÐÊµ¼ÊÊý¾Ý	  
 {
 	u8 CalKey[2]={0};//¼ÆËã³öÀ´µÄÐ£ÑéÖµ
	u8 i=0;
	u16 cnt=0;
	u16 dizhi=0;
	LRC_Cal(&Rcv_Buffer[1],Recivecount-5, CalKey);//×îºóËÄÎ»ÊÇLRCºÍ
	if(CalKey[0]==Rcv_Buffer[Recivecount-4]&& CalKey[1]==Rcv_Buffer[Recivecount-3]//¼ÆËã³öµÄÐ£ÑéÓë½ÓÊÕµ½µÄLRC±È½ÏÕýºÃÔÚµ¹ÊýµÚ3¸ö
		&&Rcv_Buffer[1]==(0 + '0')&&Rcv_Buffer[2]==(1 + '0'))//Èç¹ûÐ£ÑéÕýÈ·,Í¬Ê±µ¥ÔªºÅÕýÈ·
	{
     dizhi=AsciiToHex(Rcv_Buffer[5])*16*16*16+AsciiToHex(Rcv_Buffer[6])*16*16
										+AsciiToHex(Rcv_Buffer[7])*16+AsciiToHex(Rcv_Buffer[8]);
	   cnt=AsciiToHex(Rcv_Buffer[9])*16*16*16+AsciiToHex(Rcv_Buffer[10])*16*16
										+AsciiToHex(Rcv_Buffer[11])*16+AsciiToHex(Rcv_Buffer[12]);
			if(Rcv_Buffer[3]==(1+'0') && Rcv_Buffer[4]==(0+'0'))//ÊÇ¸øÎÒÐ´Êý¾Ý
			{
				                                   //Ö÷»ú¸øÎÒÊý¾Ý£¬ÎÒÏìÓ¦Ëû
				if(dizhi==1000)//×ÜÌåÉèÖÃ£¬´«µ¥ÏßÐÔÒÔ¼°Ë«ÏßÐÔ
				{
					Rcv_For_User[1]=AsciiToHex(Rcv_Buffer[15])*16*16*16+AsciiToHex(Rcv_Buffer[16])*16*16
										+AsciiToHex(Rcv_Buffer[17])*16+AsciiToHex(Rcv_Buffer[18+i*4]);
				}
				 if(dizhi>=1100 && dizhi<=1109)
				{
					for(i=0;i<cnt;i++)//½«Êý¾Ý×ª´æµ½Rcv_For_UserÊý×éÄÚ¹©ÓÃ»§Ê¹ÓÃ
					{								   //°Ñ×Ö·û×ª»»³ÉÊý×Ö
						Rcv_For_User[i+dizhi-1098]=AsciiToHex(Rcv_Buffer[15+i*4])*16*16*16+AsciiToHex(Rcv_Buffer[16+i*4])*16*16
										+AsciiToHex(Rcv_Buffer[17+i*4])*16+AsciiToHex(Rcv_Buffer[18+i*4]);
						//·µ»ØÖµÓ¦¸ÃÊÇ´ÓµÚ8¸ö¿ªÊ¼8*16*16*16+9*16*16+10*16+11
					}
				}
				 if(dizhi>=1200 && dizhi<=1209)
				{
					for(i=0;i<cnt;i++)//½«Êý¾Ý×ª´æµ½Rcv_For_UserÊý×éÄÚ¹©ÓÃ»§Ê¹ÓÃ
					{								   //°Ñ×Ö·û×ª»»³ÉÊý×Ö
						Rcv_For_User[i+dizhi-1188]=AsciiToHex(Rcv_Buffer[15+i*4])*16*16*16+AsciiToHex(Rcv_Buffer[16+i*4])*16*16
										+AsciiToHex(Rcv_Buffer[17+i*4])*16+AsciiToHex(Rcv_Buffer[18+i*4]);
					}
				}			
				 if(dizhi>=1300 && dizhi<=1309)
				{
					for(i=0;i<cnt;i++)//½«Êý¾Ý×ª´æµ½Rcv_For_UserÊý×éÄÚ¹©ÓÃ»§Ê¹ÓÃ
					{								   //°Ñ×Ö·û×ª»»³ÉÊý×Ö
						Rcv_For_User[i+dizhi-1278]=AsciiToHex(Rcv_Buffer[15+i*4])*16*16*16+AsciiToHex(Rcv_Buffer[16+i*4])*16*16
										+AsciiToHex(Rcv_Buffer[17+i*4])*16+AsciiToHex(Rcv_Buffer[18+i*4]);
					}
				}	
				 if(dizhi>=1400 && dizhi<=1409)
				{
					for(i=0;i<cnt;i++)//½«Êý¾Ý×ª´æµ½Rcv_For_UserÊý×éÄÚ¹©ÓÃ»§Ê¹ÓÃ
					{								   //°Ñ×Ö·û×ª»»³ÉÊý×Ö
						Rcv_For_User[i+dizhi-1368]=AsciiToHex(Rcv_Buffer[15+i*4])*16*16*16+AsciiToHex(Rcv_Buffer[16+i*4])*16*16
										+AsciiToHex(Rcv_Buffer[17+i*4])*16+AsciiToHex(Rcv_Buffer[18+i*4]);
					}
				}	
					if(dizhi>=2011 && dizhi<=2012)
				{
					for(i=0;i<cnt;i++)//½«Êý¾Ý×ª´æµ½Rcv_For_UserÊý×éÄÚ¹©ÓÃ»§Ê¹ÓÃ
					{								   //°Ñ×Ö·û×ª»»³ÉÊý×Ö
						Rcv_For_User[i+dizhi-1969]=AsciiToHex(Rcv_Buffer[15+i*4])*16*16*16+AsciiToHex(Rcv_Buffer[16+i*4])*16*16
										+AsciiToHex(Rcv_Buffer[17+i*4])*16+AsciiToHex(Rcv_Buffer[18+i*4]);
					}
				}	
				if(dizhi>=2021 && dizhi<=2022)
				{
					for(i=0;i<cnt;i++)//½«Êý¾Ý×ª´æµ½Rcv_For_UserÊý×éÄÚ¹©ÓÃ»§Ê¹ÓÃ
					{								   //°Ñ×Ö·û×ª»»³ÉÊý×Ö
						Rcv_For_User[i+dizhi-1977]=AsciiToHex(Rcv_Buffer[15+i*4])*16*16*16+AsciiToHex(Rcv_Buffer[16+i*4])*16*16
										+AsciiToHex(Rcv_Buffer[17+i*4])*16+AsciiToHex(Rcv_Buffer[18+i*4]);
					}
				}	
				if(dizhi>=2031 && dizhi <=2032)
				{
					for(i=0;i<cnt;i++)//½«Êý¾Ý×ª´æµ½Rcv_For_UserÊý×éÄÚ¹©ÓÃ»§Ê¹ÓÃ
					{								   //°Ñ×Ö·û×ª»»³ÉÊý×Ö
						Rcv_For_User[i+dizhi-1985]=AsciiToHex(Rcv_Buffer[15+i*4])*16*16*16+AsciiToHex(Rcv_Buffer[16+i*4])*16*16
										+AsciiToHex(Rcv_Buffer[17+i*4])*16+AsciiToHex(Rcv_Buffer[18+i*4]);
					}
				}	
				if(dizhi>=2041 && dizhi<=2042)
				{
					for(i=0;i<cnt;i++)//½«Êý¾Ý×ª´æµ½Rcv_For_UserÊý×éÄÚ¹©ÓÃ»§Ê¹ÓÃ
					{								   //°Ñ×Ö·û×ª»»³ÉÊý×Ö
						Rcv_For_User[i+dizhi-1993]=AsciiToHex(Rcv_Buffer[15+i*4])*16*16*16+AsciiToHex(Rcv_Buffer[16+i*4])*16*16
										+AsciiToHex(Rcv_Buffer[17+i*4])*16+AsciiToHex(Rcv_Buffer[18+i*4]);
					}
				}		
      if(dizhi==2100)
				{
						Rcv_For_User[50]=AsciiToHex(Rcv_Buffer[18]);//EEPROM
				}	
				SendFlag=0;  //ÏÈÔÝÊ±·ÅÔÚÕâ¶ù 	
        Rcv_For_User[0]=1;//±íÊ¾Êý¾ÝÒÑ¾­¸úÐÂ£¬¿ÉÒÔÊ¹ÓÃ				
				
			}
		  if(Rcv_Buffer[3]==(0+'0') &&Rcv_Buffer[4]==(3+'0'))
			{
				SendFlag=1;      //Èç¹ûÖ÷»úÒª¿´ÎÒµÄÊý¾Ý£¬ÎÒ¾ÍÖ±½ÓÐ´¸øËü
			}
			
	}		
 }

                                                                                    							
void Respond_Read(void) //Ö÷»úÏòÎÒÐ´Êý¾Ý£¬ÎÒÒª·¢17¸öASCIIÂë
 {
 	u8 CalKey[2]={0};  //¼ÆËã³öÀ´µÄÐ£ÑéÖµ  ÎÒÕâ¸öµ±µÄÐ£ÑéÖµÖ»ÓÐÒ»Î»  
	Sendcount= 17;	
	Send_Buffer[0]=':';//´æ·ÅÖ¡¿ªÊ¼±êÖ¾£¨ÎÒÃÇÕâ¶ùÓ¦¸ÃÊÇ':')   //modified
	Send_Buffer[1]=0 + '0';//Õ¾ºÅ
	Send_Buffer[2]=1 + '0';
 	Send_Buffer[3]=1+'0';	 	//¹¦ÄÜÂëÎª10
	Send_Buffer[4]=0+'0';   


	//ÏÈÌîÐ´Ê×µØÖ·
	Send_Buffer[5]=Rcv_Buffer[5];   
	Send_Buffer[6]=Rcv_Buffer[6];
	Send_Buffer[7]=Rcv_Buffer[7];
	Send_Buffer[8]=Rcv_Buffer[8];

	//ÔÙÌîÐ´Òª¶ÁµÄ×ÖÊý
	Send_Buffer[9]=Rcv_Buffer[9];
	Send_Buffer[10]=Rcv_Buffer[10];
	Send_Buffer[11]=Rcv_Buffer[11]; 
	Send_Buffer[12]=Rcv_Buffer[12];	
	LRC_Cal(&Send_Buffer[1],Sendcount-4,CalKey);// i just minus 1
	Send_Buffer[13]=CalKey[0];                 
	Send_Buffer[14]=CalKey[1];
	//ÌîÐ´½áÊø·û
	Send_Buffer[15]='\r';
	Send_Buffer[16]='\n';
//Send_Buffer[0]=0;
//Send_Buffer[1]=1;
//Send_Buffer[2]=2;
//Send_Buffer[3]=3;
//Send_Buffer[4]=4;
	Send_Index = 0;
	USART_ITConfig(USART3, USART_IT_TC, ENABLE);//¿ªÆôÊý¾Ý·¢ËÍÖÐ¶Ï
 }


 /* º¯Êý¹¦ÄÜ£º¶ÁÈ¡ÉèÖÃÄ£Ê½ÏÂÎÒµÄ·´À¡()
 															                                                                                         */
 void Respond_Write(void)//
 {
	
 	u8 CalKey[2]={0};//¼ÆËã³öÀ´µÄÐ£ÑéÖµ
	u16 temp = 0;//ÁÙÊ±¼ÆËãÓÃ
	u16 cnt=0;   //¼ÆËã´«ÊäÊý¾ÝËùÓÃ
	u8 cnt1,cnt2=0;
	u16 dizhi=0;
	u8 i = 0;//Ñ­»·×¨ÓÃ
	dizhi=AsciiToHex(Rcv_Buffer[5])*16*16*16+AsciiToHex(Rcv_Buffer[6])*16*16
										+AsciiToHex(Rcv_Buffer[7])*16+AsciiToHex(Rcv_Buffer[8]);
	cnt=AsciiToHex(Rcv_Buffer[9])*16*16*16+AsciiToHex(Rcv_Buffer[10])*16*16
										+AsciiToHex(Rcv_Buffer[11])*16+AsciiToHex(Rcv_Buffer[12]);
	//ÌîÐ´ÒªÐ´ÈëµÄ×ÖÄÚÈÝ
	Sendvalue_Init();
	if(Send_For_User[0]==1)//Èç¹û·¢ËÍÊý×éµÄÄÚÈÝÒÑ¾­¸úÐÂ¹ý
	{
	
		Sendcount=11+4*cnt;//
		Send_Buffer[0]=':';//´æ·ÅÖ¡¿ªÊ¼±êÖ¾£¨ÎÒÃÇÕâ¶ùÓ¦¸ÃÊÇ':')   //modified
		Send_Buffer[1]=0 + '0';//Õ¾ºÅ
		Send_Buffer[2]=1 + '0';
	 	Send_Buffer[3]=0+'0';	//	
		Send_Buffer[4]=3+'0';
	
	
		//ÏÈÌîÐ´×Ö½ÚÊý·
			cnt1=(cnt*2)/16;
			cnt2=(cnt*2)%16;
		  HexToAscii(&cnt1);
			HexToAscii(&cnt2);
			Send_Buffer[5]=cnt1; 
			Send_Buffer[6]=cnt2;
		

		for(i=0;i<cnt;i++)//½«Êý¾Ý×ª´æµ½Rcv_For_UserÊý×éÄÚ¹©ÓÃ»§Ê¹ÓÃ
		{
			if(dizhi==1000)//Òª¿´Ä£ÄâÍ¨µÀÇúÏßÄ£Ê½
			{
				temp = Send_For_User[69];
				Send_Buffer[7+i*4] = temp/(16*16*16);
				HexToAscii(&Send_Buffer[7+i*4]);
				temp %= 16*16*16;
				
				Send_Buffer[8+i*4] = temp/(16*16);
				HexToAscii(&Send_Buffer[8+i*4]);
				temp %= 16*16;
				Send_Buffer[9+i*4] = temp/(16);
				HexToAscii(&Send_Buffer[9+i*4]);
				temp %= 16;
				Send_Buffer[10+i*4] = temp;
				HexToAscii(&Send_Buffer[10+i*4]);
			}
			else if(dizhi>=1100 && dizhi<=1109)
			{
				temp = Send_For_User[dizhi-1099+i];
				Send_Buffer[7+i*4] = temp/(16*16*16);
				HexToAscii(&Send_Buffer[7+i*4]);
				temp %= 16*16*16;
				
				Send_Buffer[8+i*4] = temp/(16*16);
				HexToAscii(&Send_Buffer[8+i*4]);
				temp %= 16*16;
				Send_Buffer[9+i*4] = temp/(16);
				HexToAscii(&Send_Buffer[9+i*4]);
				temp %= 16;
				Send_Buffer[10+i*4] = temp;
				HexToAscii(&Send_Buffer[10+i*4]);
			}
				else if(dizhi>=1200 && dizhi<=1209)
				{
					temp = Send_For_User[dizhi-1189+i];
					Send_Buffer[7+i*4] = temp/(16*16*16);
					HexToAscii(&Send_Buffer[7+i*4]);
					temp %= 16*16*16;
					
					Send_Buffer[8+i*4] = temp/(16*16);
					HexToAscii(&Send_Buffer[8+i*4]);
					temp %= 16*16;
					Send_Buffer[9+i*4] = temp/(16);
					HexToAscii(&Send_Buffer[9+i*4]);
					temp %= 16;
					Send_Buffer[10+i*4] = temp;
					HexToAscii(&Send_Buffer[10+i*4]);
				}
				else if(dizhi>=1300 && dizhi<=1309)
				{
					temp = Send_For_User[dizhi-1279+i];
					Send_Buffer[7+i*4] = temp/(16*16*16);
					HexToAscii(&Send_Buffer[7+i*4]);
					temp %= 16*16*16;
					
					Send_Buffer[8+i*4] = temp/(16*16);
					HexToAscii(&Send_Buffer[8+i*4]);
					temp %= 16*16;
					Send_Buffer[9+i*4] = temp/(16);
					HexToAscii(&Send_Buffer[9+i*4]);
					temp %= 16;
					Send_Buffer[10+i*4] = temp;
					HexToAscii(&Send_Buffer[10+i*4]);
				}
				else if(dizhi>=1400 && dizhi<=1409)
				{
					temp = Send_For_User[dizhi-1369+i];
					Send_Buffer[7+i*4] = temp/(16*16*16);
					HexToAscii(&Send_Buffer[7+i*4]);
					temp %= 16*16*16;
					
					Send_Buffer[8+i*4] = temp/(16*16);
					HexToAscii(&Send_Buffer[8+i*4]);
					temp %= 16*16;
					Send_Buffer[9+i*4] = temp/(16);
					HexToAscii(&Send_Buffer[9+i*4]);
					temp %= 16;
					Send_Buffer[10+i*4] = temp;
					HexToAscii(&Send_Buffer[10+i*4]);
				}
				else if(dizhi>=2011 && dizhi<=2012)
				{
					temp = Send_For_User[dizhi-1970+i];
					Send_Buffer[7+i*4] = temp/(16*16*16);
					HexToAscii(&Send_Buffer[7+i*4]);
					temp %= 16*16*16;
					
					Send_Buffer[8+i*4] = temp/(16*16);
					HexToAscii(&Send_Buffer[8+i*4]);
					temp %= 16*16;
					Send_Buffer[9+i*4] = temp/(16);
					HexToAscii(&Send_Buffer[9+i*4]);
					temp %= 16;
					Send_Buffer[10+i*4] = temp;
					HexToAscii(&Send_Buffer[10+i*4]);
				}
					else if(dizhi>=2021 && dizhi<=2022)
				{
					temp = Send_For_User[dizhi-1978+i];
					Send_Buffer[7+i*4] = temp/(16*16*16);
					HexToAscii(&Send_Buffer[7+i*4]);
					temp %= 16*16*16;
					
					Send_Buffer[8+i*4] = temp/(16*16);
					HexToAscii(&Send_Buffer[8+i*4]);
					temp %= 16*16;
					Send_Buffer[9+i*4] = temp/(16);
					HexToAscii(&Send_Buffer[9+i*4]);
					temp %= 16;
					Send_Buffer[10+i*4] = temp;
					HexToAscii(&Send_Buffer[10+i*4]);
				}
					else if(dizhi>=2031 && dizhi<=2032)
				{
					temp = Send_For_User[dizhi-1986+i];
					Send_Buffer[7+i*4] = temp/(16*16*16);
					HexToAscii(&Send_Buffer[7+i*4]);
					temp %= 16*16*16;
					
					Send_Buffer[8+i*4] = temp/(16*16);
					HexToAscii(&Send_Buffer[8+i*4]);
					temp %= 16*16;
					Send_Buffer[9+i*4] = temp/(16);
					HexToAscii(&Send_Buffer[9+i*4]);
					temp %= 16;
					Send_Buffer[10+i*4] = temp;
					HexToAscii(&Send_Buffer[10+i*4]);
				}
				else if(dizhi>=2041 && dizhi<=2042)
				{
					temp = Send_For_User[dizhi-1994+i];
					Send_Buffer[7+i*4] = temp/(16*16*16);
					HexToAscii(&Send_Buffer[7+i*4]);
					temp %= 16*16*16;
					
					Send_Buffer[8+i*4] = temp/(16*16);
					HexToAscii(&Send_Buffer[8+i*4]);
					temp %= 16*16;
					Send_Buffer[9+i*4] = temp/(16);
					HexToAscii(&Send_Buffer[9+i*4]);
					temp %= 16;
					Send_Buffer[10+i*4] = temp;
					HexToAscii(&Send_Buffer[10+i*4]);
				}
				
					else if(dizhi>=3000&& dizhi<=3019)
				{
					temp = Send_For_User[dizhi-2951+i];
					Send_Buffer[7+i*4] = temp/(16*16*16);
					HexToAscii(&Send_Buffer[7+i*4]);
					temp %= 16*16*16;
					
					Send_Buffer[8+i*4] = temp/(16*16);
					HexToAscii(&Send_Buffer[8+i*4]);
					temp %= 16*16;
					Send_Buffer[9+i*4] = temp/(16);
					HexToAscii(&Send_Buffer[9+i*4]);
					temp %= 16;
					Send_Buffer[10+i*4] = temp;
					HexToAscii(&Send_Buffer[10+i*4]);
				}
			
		}	
	//	Send_For_User[0]=0;
		LRC_Cal(&Send_Buffer[1],Sendcount-4,CalKey);  //¼ÆËãLRCÐ£ÑéÂë
		Send_Buffer[7+4*cnt]=CalKey[0];
		Send_Buffer[8+4*cnt]=CalKey[1];
		
		//ÌîÐ´½áÊø·û
		Send_Buffer[9+4*cnt]='\r';
		Send_Buffer[10+4*cnt]='\n';

		Send_Index = 0;
		USART_ITConfig(USART3, USART_IT_TC, ENABLE);//¿ªÆôÊý¾Ý·¢ËÍÖÐ¶Ï
	}		    
 }




void Sendvalue_Init(void) 
{
	
	if(Send_For_User[0]==0)
	{
		Send_For_User[1]=(u16)(I1_1);    //µÚÒ»Â·Í¨µÀÕýÏò¼«ÏÞµçÁ÷
 		Send_For_User[2]=(u16)(LIM_1);   //µÚÒ»Â·Í¨µÀÕýÏò¼«ÏÞLIM+
		Send_For_User[3]=(u16)(IC_1);    //Í¨µÀÕýÏòÇÐ»»µçÁ÷IC+
		Send_For_User[4]=(u16)(C_1);     //Í¨µÀÕýÏòÇÐ»»µãC+
		Send_For_User[5]=(u16)(D_1);     //Í¨µÀÕýÏòËÀÇøD+
		
		Send_For_User[6]=(u16)(I1_2);
		Send_For_User[7]=(u16)(LIM_2);
		Send_For_User[8]=(u16)(IC_2);
		Send_For_User[9]=(u16)(C_2);
		Send_For_User[10]=(u16)(D_2);
		
		Send_For_User[11]=(u16)(I1_3);
		Send_For_User[12]=(u16)(LIM_3);
		Send_For_User[13]=(u16)(IC_3);
		Send_For_User[14]=(u16)(C_3);
		Send_For_User[15]=(u16)(D_3);
		
		Send_For_User[16]=(u16)(I1_4);
		Send_For_User[17]=(u16)(LIM_4);
		Send_For_User[18]=(u16)(IC_4);
		Send_For_User[19]=(u16)(C_4);
		Send_For_User[20]=(u16)(D_4);
		
		Send_For_User[21]=(u16)(I1_5);
		Send_For_User[22]=(u16)(LIM_5);
		Send_For_User[23]=(u16)(IC_5);
		Send_For_User[24]=(u16)(C_5);
		Send_For_User[25]=(u16)(D_5);
		
		Send_For_User[26]=(u16)(I1_6);
		Send_For_User[27]=(u16)(LIM_6);
		Send_For_User[28]=(u16)(IC_6);
		Send_For_User[29]=(u16)(C_6);
		Send_For_User[30]=(u16)(D_6);
		
		Send_For_User[31]=(u16)(I1_7);
		Send_For_User[32]=(u16)(LIM_7);
		Send_For_User[33]=(u16)(IC_7);
		Send_For_User[34]=(u16)(C_7);
		Send_For_User[35]=(u16)(D_7);
		
		Send_For_User[36]=(u16)(I1_8);
		Send_For_User[37]=(u16)(LIM_8);
		Send_For_User[38]=(u16)(IC_8);
		Send_For_User[39]=(u16)(C_8);
		Send_For_User[40]=(u16)(D_8);
		
		Send_For_User[41]=(u16)(T1_1);   
		Send_For_User[42]=(u16)(T1_2);
		Send_For_User[43]=(u16)(T2_1);
		Send_For_User[44]=(u16)(T2_2);
		Send_For_User[45]=(u16)(T3_1);   //²Ù×Ý¿ª¹Ø¼ÓËÙÊ±¼ä
		Send_For_User[46]=(u16)(T3_2);
		Send_For_User[47]=(u16)(T4_1);
		Send_For_User[48]=(u16)(T4_2);
		KAIGUANLIANG=GPIO_ReadInputDataBit(GPIOA, GPIO_Pin_11)+GPIO_ReadInputDataBit(GPIOC, GPIO_Pin_11)*2+
		            GPIO_ReadInputDataBit(GPIOC, GPIO_Pin_12)*4+GPIO_ReadInputDataBit(GPIOD, GPIO_Pin_0)*8+
								GPIO_ReadInputDataBit(GPIOD, GPIO_Pin_4)*16+GPIO_ReadInputDataBit(GPIOD, GPIO_Pin_3)*32+
							  XH1*64;
		Send_For_User[49]=(u16)(KAIGUANLIANG);//¿ª¹ØÁ¿×´Ì¬
		Send_For_User[50]=(u16)(I_FEEDBACK1);  //±ÈÀýµç´ÅÌúµç1
		Send_For_User[51]=(u16)(I_FEEDBACK2);  //±ÈÀýµç´ÅÌúµç2
		Send_For_User[52]=(u16)(I_FEEDBACK3);  //±ÈÀýµç´ÅÌúµç3
		Send_For_User[53]=(u16)(I_FEEDBACK4);  //±ÈÀýµç´ÅÌúµç4
		Send_For_User[54]=(u16)(I_FEEDBACK5);  //±ÈÀýµç´ÅÌúµç5
		Send_For_User[55]=(u16)(I_FEEDBACK6);  //±ÈÀýµç´ÅÌúµç6
		Send_For_User[56]=(u16)(I_FEEDBACK7);  //±ÈÀýµç´ÅÌúµç7
		Send_For_User[57]=(u16)(I_FEEDBACK8);  //±ÈÀýµç´ÅÌúµç8
		Send_For_User[58]=(u16)(I_FEEDBACK9);  //¿ª¹Øµç´ÅÌú1
		Send_For_User[59]=(u16)(I_FEEDBACK10);  //¿ª¹Øµç´ÅÌú2
		Send_For_User[60]=(u16)(I_FEEDBACK11);  //¿ª¹Øµç´ÅÌú3
		Send_For_User[61]=(u16)(I_FEEDBACK12);  //¿ª¹Øµç´ÅÌú4
		

		Send_For_User[62]=(u16)(V_DONGBI/10);      //µçÎ»Æ÷µçÑ¹1
		Send_For_User[63]=(u16)(V_FANDOU/10);      //µçÎ»Æ÷µçÑ¹2
		Send_For_User[64]=(u16)(V_FUZHU1/10);      //µçÎ»Æ÷µçÑ¹3
		Send_For_User[65]=(u16)(V_FUZHU2/10);      //µçÎ»Æ÷µçÑ¹4
		DLBJ=DL1| (DL2<<1) | (DL3<<2)|(DL4<<3) |(DL5<<4) | (DL6<<5)	
		|(DL7<<6)| (DL8<<7) | (DL9<<8)|(DL10<<9) |(DL11<<10) | (DL12<<11);	
		Send_For_User[66]=(u16)(DLBJ);
		dlBJ=dBJ(I_FEEDBACK1)| (dBJ(I_FEEDBACK2)<<1) | (dBJ(I_FEEDBACK3)<<2)|(dBJ(I_FEEDBACK4)<<3) |(dBJ(I_FEEDBACK5)<<4) | (dBJ(I_FEEDBACK6)<<5)	
		|(dBJ(I_FEEDBACK7)<<6)| (dBJ(I_FEEDBACK8)<<7) | (dBJ(I_FEEDBACK9)<<8)|(dBJ(I_FEEDBACK10)<<9) |(dBJ(I_FEEDBACK11)<<10) | (dBJ(I_FEEDBACK12)<<11);	
		if(dlBJ>0)
			BJBZ2=1;
		else if(dlBJ==0)
			BJBZ2=0;
		Send_For_User[67]=(u16)(dlBJ);
		if(DWBJ % 8 >0 || DLBJ>0 )
			BJBZ1=1;
		else
			BJBZ1=0;
		DWBJ=DDBJ(V_DONGBI)| (DDBJ(V_FANDOU)<<1) | (DDBJ(V_FUZHU1)<<2)|(DDBJ(V_FUZHU2)<<3) |(XBJ(V_ZONG)<<4) |(XBJ1(V_ZONG)<<5) |(get_temperature()<<6);
		Send_For_User[68]=(u16)(DWBJ);
		Send_For_User[69]=(u16)(LINEAR1+LINEAR2*2+LINEAR3*4+LINEAR4*8+ZUOYI*256+XIEHE*512+YALI*1024);  //Ä£ÄâÍ¨µÀÇúÏß×´Ì¬
		Send_For_User[0]=1;
	

	}
}
