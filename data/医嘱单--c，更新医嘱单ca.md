
## 远程南溪

南溪人民远程号
490016664
hz111111


## 复制南溪医嘱单程序

### 增加方法

DHCCDoctorOrderSheet.BLL.DoctorOrderSheetBLL

```
        public static string GetUserSignImage(string userId)
        {
            string result = DoctorOrderSheetDAO.GetUserSignImage(userId);
            return result;
        }
```

DHCCDoctorOrderSheet.BLL.DoctorOrderSheetDAO

```
        public static string GetUserSignImage(string userId)
        {
            return DHCCConnection.DHCCConnection.getData("web.DHCNurSignVerify", "GetUserSignImage", userId);
        }
```

DHCCDoctorOrderSheet.Doctor.DoctorPrintData

```
private Dictionary<string, Image> ImageData;

        public DoctorPrintData(XmlElement xmlConfig, int reformTimes,string parref,string reformTime)
        {
            this.pageNo = 1;
            this.rowNo = 0;
            this.lastPrintPageNo = 0;
            this.lastPrintRowNo = 0;
            this.lastPrintOrderCreateDateTime = 0;
            this.signPrintFlag = false;
            this.seePrintFlag = false;
            this.ReformTimes = reformTimes;
            this.ReformTime = reformTime;
            this.parref=parref;
            this.CanReform = true;
            this.printHistoryData = new ArrayList();
            this.nextPageArcimDesc = new ArrayList();
            this.printData = new Dictionary<int, DoctorOrderPageData>();
            this.addedData = new Dictionary<string,DoctorOrderRowData>();
            this.ImageData = new Dictionary<string, Image>();
            this.initConfig(xmlConfig);
        }
```

### 增加方法drawUser

```
        public void drawUser(Graphics g, DoctorOrderCellData cellData, DoctorOrderColumnConfig columnConfig, ref bool ifDraw)
        {
            if (cellData.Value.IndexOf("*") < 0)
            {
                this.drawCell(g, cellData, columnConfig, ref ifDraw);
                return;
            }
            string userName = cellData.Value.Split('*')[0];
            string userId = cellData.Value.Split('*')[1];
            if (this.signPrintFlag && (cellData.PrintingFlag == false))
            {
                return;
            }
            float width = columnConfig.Width;
            float height = columnConfig.ContentHeight;
            float x = cellData.X;
            float y = cellData.Y;
            Brush brush = columnConfig.Brush;
            if (this.seePrintFlag)

            {

                if (cellData.PrintingFlag)
                {
                    brush = columnConfig.ViewBrush;
                }

            }
            else
            {
                x = x - this.hardMarginX;
                y = y - this.hardMarginY;
            }
            Color color = ((System.Drawing.SolidBrush)brush).Color;


            if (userId != "")
            {
                Image image = null;
                if (userId.IndexOf("/") > -1)
                {

                    string[] users = userId.Split('/');
                    try
                    {
                        if ((ImageData != null) && (ImageData.ContainsKey(users[0])))
                        {
                            image = ImageData[users[0]];
                        }
                        else
                        {
                            image = GetUserImageColored(users[0], color);
                            ImageData.Add(users[0], image);
                        }
                        //image = GetUserImageColored(users[0], color);
                        if (image == null)
                        {
                            cellData.Value = cellData.Value.Split('*')[0];
                            this.drawCell(g, cellData, columnConfig, ref ifDraw);
                        }
                        else
                        {
                            g.DrawImage(image, x, y, (width / 2) - 1, height);
                        }
                        if ((ImageData != null) && (ImageData.ContainsKey(users[1])))
                        {
                            image = ImageData[users[1]];
                        }
                        else
                        {
                            image = GetUserImageColored(users[1], color);
                            ImageData.Add(users[1], image);
                        }
                        //image = GetUserImageColored(users[1], color);
                        if (image == null)
                        {
                            cellData.Value = cellData.Value.Split('*')[0];
                            this.drawCell(g, cellData, columnConfig, ref ifDraw);
                        }
                        else
                        {
                            g.DrawImage(image, x + 1 + width / 2, y, width / 2, height);
                        }
                    }
                    catch
                    {
                    }

                }
                else
                {
                    try
                    {

                        if ((ImageData != null) && (ImageData.ContainsKey(userId)))
                        {
                            image = ImageData[userId];
                        }
                        else
                        {
                            image = GetUserImageColored(userId, color);
                            ImageData.Add(userId, image);
                        }
                        //image = GetUserImageColored(userId, color);
                        if (image == null)
                        {
                            cellData.Value = cellData.Value.Split('*')[0];
                            this.drawCell(g, cellData, columnConfig, ref ifDraw);
                        }
                        else
                        {
                            g.DrawImage(image, x, y, width, height);
                        }
                    }
                    catch
                    {
                    }

                }

            }
            ifDraw = true;
        }
        public Image GetUserImageColored(string userid, Color color)
        {
            string userpic = DoctorOrderSheetBLL.GetUserSignImage(userid);
            Image signImage = StringToImage(userpic);
            if (signImage != null)
            {
                signImage = this.changecolor(signImage, color.R, color.G, color.B);
            }
            return signImage;
        }
        public Image StringToImage(string imgstr)
        { //字符串－－〉流
            if (imgstr == "") return null;
            byte[] ChangeAfterImageBytes = Convert.FromBase64String(imgstr);//再将字符串分拆成字节数组
            MemoryStream ChangeAfterMS = new MemoryStream(ChangeAfterImageBytes);//将字节数组保存到新的内存流上
            Image Image = Image.FromStream(ChangeAfterMS);//将内存流保存成一张图片
            return Image;
        }
        public Bitmap changecolor(Image SImage, int l1, int l2, int l3)
        {
            int Height = SImage.Height;
            int Width = SImage.Width;
            Bitmap bitmap = new Bitmap(Width, Height);
            Bitmap MyBitmap = (Bitmap)SImage;
            for (int x = 0; x < Width; x++)
            {
                for (int y = 0; y < Height; y++)
                {
                    Color pixel1 = MyBitmap.GetPixel(x, y);
                    int r = pixel1.R;
                    int g = pixel1.G;
                    int b = pixel1.B;
                    if (r + g + b < 250 * 3)
                    {
                        r = l1;
                        g = l2;
                        b = l3;
                        if (r > 255)
                        {
                            r = 255;
                        }
                        if (r < 0)
                        {
                            r = 0;
                        }
                        if (g > 255)
                        {
                            g = 255;
                        }
                        if (g < 0)
                        {
                            g = 0;
                        }
                        if (b > 255)
                        {
                            b = 255;
                        }
                        if (b < 0)
                        {
                            b = 0;
                        }
                        bitmap.SetPixel(x, y, Color.FromArgb(r, g, b));
                    }
                }
            }
            return bitmap;
        }
```

### 修改方法

```
                            if (cellNo == 0)
                            {
                                this.firstCellX = cellData.X;
                                cellNo = 1;
                            }
                            switch (cellData.Code)
                            {
                                case "Doctor":
                                case "SeeNurse":
                                case "ExcuteNurse":
                                case "StopDoctor":
                                case "StopNurse":
                                    this.drawUser(g, cellData, columnCofig, ref ifDraw);
                                    break;
                                default:
                                    this.drawCell(g, cellData, columnCofig, ref ifDraw);
                                    break;
                            }
```

![[Pasted image 20231120102959.png]]

## 复制南溪后台方法程序

### 修改临时医嘱单

//zhu ca

```
/// 临时医嘱query

ClassMethod FindTempOrderExecute(ByRef qHandle As %Binary, episodId As %String, locId As %String, startDate As %String, startTime As %String, endDate As %String, endTime As %String) As %Status
{
	s ^EH("FindXXXXOrder","Temp")=$lb(episodId , locId , startDate , startTime , endDate , endTime)
	//d ##class(%ResultSet).RunQuery("Nur.DoctorOrderSheet","FindTempOrder","91508","106","2020-10-10","14:57:22","","")
	s repid=$i(^CacheTemp)
	s ind=1
	i (episodId="")!(locId="") s qHandle=$lb(0,repid,0) q $$$OK
	///日期和时间格式取配置
	s dateType=3,timeType=2
	s dateType = +$g(^DHCOEOrdPrintSet("dateType"))
	s timeType = +$g(^DHCOEOrdPrintSet("timeType"))
	 
	s oeordId=$o(^OEORD(0,"Adm",episodId,""))
	s admType=$p(^PAADM(episodId),"^",2)
	//s groupId = $g(^OrderSheetTemp(userID))
	//zhu ca
 	s CAFlag=##Class(web.DHCNurCASignVerify).GetIsVerifyCA(locId)
 	i oeordId="" s qHandle=$lb(0,repid,0) q $$$OK
 	s startDateTime=$zdh(startDate,3)*100000+$zth(startTime)
 	i endDate'="" s endDateTime=$zdh(endDate,3)*100000+$zth(endTime)
 	e  s endDateTime=""
 	s oeordSub=0 f  s oeordSub=$o(^OEORD(oeordId,"I",oeordSub)) q:oeordSub=""  d
 	.q:$g(^OEORD(oeordId,"I",oeordSub,1))=""
 	.i (oeordId_"||"_oeordSub)=("615||5") b
 	.s ordDep=$p(^OEORD(oeordId,"I",oeordSub,1),"^",3)     //下医嘱科室
 	.;q:ordDep'=locId
 	.s orderCreateDate=$p(^OEORD(oeordId,"I",oeordSub,3),"^",7)  //下医嘱日期
 	.s orderCreateTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",17)
 	.s orderInsertDate=$p(^OEORD(oeordId,"I",oeordSub,1),"^",19)  //插医嘱日期 儿童用
 	.s orderInsertTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",20)
 	.i (orderInsertDate'="")&&(orderInsertTime'="") d
 	..;s orderCreateTime=orderInsertTime
 	..;s orderCreateDate=orderInsertDate
 	.s orderCreateDateTime=orderCreateDate*100000+orderCreateTime
 	.q:orderCreateDateTime<startDateTime
 	.q:(endDateTime'="")&(orderCreateDateTime>endDateTime)
 	.s orderSttDate=$p(^OEORD(oeordId,"I",oeordSub,1),"^",9)  //医嘱开始日期
 	.s orderSttTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",10) 
 	.s priorDr=$P($G(^OEORD(oeordId,"I",oeordSub,1)),"^",8)   //医嘱优先级 
	.i priorDr'="" s priorDesc=$P(^OECPR(priorDr),"^",2)  
	.q:priorDr=""
	.s priorDesc="^"_priorDesc_"^"	
	.s arcimDr=$P($G(^OEORD(oeordId,"I",oeordSub,1)),"^",2)
	.//屏蔽会诊相关医嘱，除了"会诊申请"和"护理会诊"	ts20.8.11
	.s arcimDesc=$P($G(^ARCIM(+arcimDr,$p(arcimDr,"||",2),1)),"^",2)
	.q:(arcimDesc["会诊")&(arcimDr'="18445||1")&(arcimDr'="9263||1")&(episodId'="26922")
	.//临时医嘱单不显示术后医嘱、产后医嘱ts20.7.28
	.s operArcimDr=$G(^DHCOEOrdPrintSet("OperOrderArcimDr"))
	.s deliverArcimDr=$G(^DHCOEOrdPrintSet("DeliverOrderArcimDr"))
	.q:(arcimDr=operArcimDr)!(arcimDr=deliverArcimDr)
	.//ReformTimes不为空表示打印过
	.s ReformTimes=$o(^Nur.DoctorOrderPrintRecordSubI("OrderId"," "_oeordId_"||"_oeordSub,""))
	.//屏蔽医嘱状态
	.s ordStateDr=$P($G(^OEORD(oeordId,"I",oeordSub,1)),"^",13) //医嘱状态
	.q:((ordStateDr="2")!(ordStateDr=""))&(ReformTimes="")
	.q:(admType'="I")&&(ordStateDr=4)&(ReformTimes="")
	.//临时医嘱过滤
	.s ArcimDR=$p(^OEORD(oeordId,"I",oeordSub,1),"^",2)
	.//临时医嘱显示长期产后护理,为了实现临时医嘱单产后换页
	.s DeliverArcimDR=$G(^DHCOEOrdPrintSet("DeliverOrderArcimDr"))
	.s grassDrugFlag = ..ifGrassDrug(oeordId,oeordSub)
	.q:(^DHCOEOrdPrintSet("OrdTyp")'[priorDesc)&(ArcimDR'=DeliverArcimDR)&(grassDrugFlag'="Y")
	.//合并草药协定处方--南溪人医取消合并tangshuang20.7.1
#;	.s grassDrugFlag = ..ifGrassDrug(oeordId,oeordSub)
#; 	.;s prescName = ..getPrescName(oeordId,oeordSub)
#; 	.s prescSeqNo = $p(^OEORD(oeordId,"I",oeordSub,1),"^",15)
#; 	.q:(grassDrugFlag="Y")&&((+prescSeqNo)>1)  ;&&(prescName'="")
	.//屏蔽科室
	.s schLoc=$P($G(^OEORD(oeordId,"I",oeordSub,7)),"^",2)
	.s schLoc=$p(schLoc,$c(1))
	.s schLoc="^"_$G(schLoc)_"^"
	.s lsNotLoc=$G(^DHCOEOrdPrintSet("NotLoc"))
	.q:($G(^DHCOEOrdPrintSet("NotLoc"))[schLoc)&((lsNotLoc'="^^")!(lsNotLoc'=""))&(ReformTimes="")
	.//屏蔽医护人员
	.s doctorDr=$P($G(^OEORD(oeordId,"I",oeordSub,1)),"^",11)
	.if doctorDr'="" s cpTypDR=$P($g(^CTPCP(doctorDr,1)),"^",4)  ;CTPCP_CarPrvTp_DR
	.q:$g(cpTypDR)=""
	.q:'$D(^CT("CPT",cpTypDR))
	.s cpTyp=""
	.i $G(cpTypDR)'="" s cpTyp=$P($g(^CT("CPT",cpTypDR)),"^",4)  ;CT_CarPrvTp
	.q:cpTyp'="DOCTOR"
 	.//按子类和大类屏蔽医嘱
 	.s itemCatDr=$P($G(^ARCIM(+arcimDr,$p(arcimDr,"||",2),1)),"^",10)
 	.s ordCatDr=$P(^ARC("IC",itemCatDr),"^",8)    
	.s ordCat=$P(^OEC("ORCAT",ordCatDr),"^",1)    
	.s ordCatDrStr="^"_ordCatDr_"^"
	.//大类
	.q:($G(^DHCOEOrdPrintSet("NotOrdCat"))[ordCatDrStr&($G(^DHCOEOrdPrintSet("NotOrdCat"))'=""))&(ReformTimes="")
	.s ItemCatDrStr="^"_itemCatDr_"^" 
	.//子类
	.q:($G(^DHCOEOrdPrintSet("NotSordCat"))[ItemCatDrStr&($G(^DHCOEOrdPrintSet("NotSordCat"))'=""))&(ReformTimes="")
	.//撤销医嘱是否上医嘱单
	.s ordStateDr=$P($G(^OEORD(oeordId,"I",oeordSub,1)),"^",13) //医嘱状态
	.s orderState=$p(^OEC("OSTAT",ordStateDr),"^",1)
	.q:(orderState="C")&($G(^DHCOEOrdPrintSet("ifstop"))'="true")
	.//*********************************
 	.s seqNo=$P($G(^OEORD(oeordId,"I",oeordSub,3)),"^",4)  //医嘱序号
	.s relationMainOrd=$p($g(^OEORD(oeordId,"I",oeordSub,11)),"^",39) //关联主医嘱ID
    .i relationMainOrd'="" d
    ..s relationMainOrdSub=$P(relationMainOrd,"||",2) 
    .e  s relationMainOrdSub=oeordSub
    .s sortDate=$p(^OEORD(oeordId,"I",oeordSub,3),"^",7)  //下医嘱日期
 	.s sortTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",17)
    .s sortOrd(sortDate,sortTime,relationMainOrdSub,seqNo,oeordId,oeordSub)=""  //排序
    .s sortOrdId(oeordId,oeordSub)=""
    
    s orderSttDate="" f  s orderSttDate=$o(sortOrd(orderSttDate)) q:orderSttDate=""  d
    .s orderSttTime="" f  s orderSttTime=$o(sortOrd(orderSttDate,orderSttTime)) q:orderSttTime=""  d
    ..s relationMainOrdSub="" f  s relationMainOrdSub=$o(sortOrd(orderSttDate,orderSttTime,relationMainOrdSub)) q:relationMainOrdSub=""  d
    ...s seqNo="" f  s seqNo=$o(sortOrd(orderSttDate,orderSttTime,relationMainOrdSub,seqNo)) q:seqNo=""  d
    ....s oeordId="" f  s oeordId=$o(sortOrd(orderSttDate,orderSttTime,relationMainOrdSub,seqNo,oeordId)) q:oeordId=""  d
    .....s oeordSub="" f  s oeordSub=$o(sortOrd(orderSttDate,orderSttTime,relationMainOrdSub,seqNo,oeordId,oeordSub)) q:oeordSub=""  d
    ......//startDate:%String,startTime:%String,createDate:%String,createTime:%String,arcimDesc:%String,doctor:%String,excuteNurse:%String,excuteDateTime:%String,setDisconDate:%String,setDisconTime:%String,stopDoctor:%String,stopNurse:%String
    ......s startDate=$p(^OEORD(oeordId,"I",oeordSub,1),"^",9)  //医嘱开始日期
 	......s startTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",10) 
 	......s createDate=$p(^OEORD(oeordId,"I",oeordSub,3),"^",7)  //下医嘱日期
 	......s createTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",17)
 	......//医嘱名称
 	......s arcimDesc=..getOrderName(oeordId,oeordSub)
 	......i arcimDesc["罗浮山" b
 	......s oeoreNote=$p($g(^OEORD(oeordId,"I",oeordSub,"X",1)),"^",44)		//护士执行备注 ts20.8.11
 	......i oeoreNote'="" s arcimDesc=arcimDesc_" ("_oeoreNote_")"
 	......i startDate'=createDate s arcimDesc=arcimDesc_"(执行日期:"_$zd(startDate,dateType)_")"
	......//下医嘱医生
 	......s addUserDr=$P($G(^OEORD(oeordId,"I",oeordSub,7)),"^",1)
	......q:addUserDr=""
    ......s doctor=$P($G(^SSU("SSUSR",addUserDr)),"^",2)
    ......//zhu ca
    ......s:CAFlag doctor=doctor_"*"_addUserDr
    ......//实习医生
    ......s practiceDoctor=""
    ......s practiceAddUserDr=$P($G(^OEORD(oeordId,"I",oeordSub,"DHC")),"^",36)
    ......i practiceAddUserDr'="" s practiceDoctor=$P(^SSU("SSUSR",practiceAddUserDr),"^",2)
    ......i practiceDoctor'="" s doctor=practiceDoctor_"/"_doctor
    ......//审核医生
    ......s auditDoctor =..limitDrugAuditDoc(oeordId_"||"_oeordSub)
    ......s:auditDoctor'="" doctor=auditDoctor_"/"_doctor
    ......//医嘱开始日期
    ......s orderStartDate=$p(^OEORD(oeordId,"I",oeordSub,1),"^",9)  
 	......s orderStartTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",10)
	......s startDate=$zd(orderStartDate,dateType)
 	......s startTime=$zt(orderStartTime,timeType)
 	......s starteDateTime=orderStartDate*100000+orderStartTime
 	......//医嘱创建日期
 	......s orderCreateDate=$p(^OEORD(oeordId,"I",oeordSub,3),"^",7)  
 	......s orderCreateTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",17)
 	......s createDate=$zd(orderCreateDate,dateType)
 	......s createTime=$zt(orderCreateTime,timeType)
 	......s createDateTime=orderCreateDate*100000+orderCreateTime
 	......//执行医嘱信息
 	......s excuteNurse="",excuteDateTime=""
 	......s excuteInfo=..getExcuteInfo(oeordId,oeordSub)
 	......i excuteInfo'="^^" d
 	.......s excuteNurse=$p(^CTPCP($p(excuteInfo,"^",1),1),"^",2)
 	.......s excuteDateTime=$zd($p(excuteInfo,"^",2)	,3)_" "_$zt($p(excuteInfo,"^",3),2)
 	.......//zhu ca
 	.......s excuteNurseName=$p(^CTPCP($p(excuteInfo,"^",1),1),"^",2)
 	.......s excuteNurUser=$o(^SSU("SSUSR",0,"CTPCP",$p(excuteInfo,"^",1),""))
 	.......i CAFlag s excuteNurse=excuteNurseName_"*"_excuteNurUser
 	......d getTakeOrdExTime 	
 	......e  d
 	.......q:1=1
 	.......s excuteInfo=##class(web.DHCLCNUREXCUTE).GetOrderStInfo(oeordId_"||"_oeordSub,"E")
 	.......i (excuteInfo'="^^")&&($p(excuteInfo,"^",1)) d
 	........s excuteNurse=$p($g(^SSU("SSUSR",$p(excuteInfo,"^",1))),"^",2)
 	........s excuteDateTime=$zd($p(excuteInfo,"^",2),3)_" "_$zt($p(excuteInfo,"^",3),2)
 	.......e  s excuteNurse="",excuteDateTime=""
 	......//停止医嘱信息
 	......s disconInfo=##class(web.DHCLCNUREXCUTE).GetXOrdInfo(oeordId,oeordSub)
 	......i (disconInfo'="^^")&&($d(^SSU("SSUSR",$p(disconInfo,"^",1)))) d
 	.......s stopNurse=$p($g(^SSU("SSUSR",$p(disconInfo,"^",1))),"^",2)
 	.......s setDisconDate=$zd($p(disconInfo,"^",2),3)
 	.......s setDisconTime=$zt($p(disconInfo,"^",3),2)
 	......e  s stopNurse="",setDisconDate="",setDisconTime=""
 	......s stopDoctorStr=..getStopDoctor(oeordId,oeordSub)
 	......s stopDoctor=$p(stopDoctorStr,"^",1)
 	......s stopDate=$p(stopDoctorStr,"^",2)
 	......i stopDate'="" s stopDate=$TR($E(stopDate,6,10),"-","/")  
 	......s stopTime=$p(stopDoctorStr,"^",3)
 	......s OrderId=oeordId_"||"_oeordSub
 	......//医嘱状态
 	......s otherInfo=""
 	......s ordStateDr=$P($G(^OEORD(oeordId,"I",oeordSub,1)),"^",13) //医嘱状态
	......s orderState=$p(^OEC("OSTAT",ordStateDr),"^",1)
	......i orderState="U" d
	.......s otherInfo="----DC"
	.......s excuteNurse="",excuteDateTime=""
	......i orderState="C" d
	.......s otherInfo="----Cancel "_..orderCancleInfo(oeordId,oeordSub)
	.......s excuteNurse="",excuteDateTime=""
	......//成组医嘱中括号标志
	......s subChar=""
	......i $g(^DHCOEOrdPrintSet("ifShowOrderGroupFlag"))="true" s subChar=..getSubChar(oeordId, oeordSub,.sortOrdId)
 	......s OrdRedLine=""
 	......i $g(^DHCOEOrdPrintSet("ifTempOperRedLine"))="true" d
	.......s ArcimDR=$p(^OEORD(oeordId,"I",oeordSub,1),"^",2)
	.......s OperArcimDR=$G(^DHCOEOrdPrintSet("OperOrderArcimDr"))	
	.......i ArcimDR=OperArcimDR s OrdRedLine="Top"
	......i $g(^DHCOEOrdPrintSet("ifTempDeliverRedLine"))="true" d
	.......s ArcimDR=$p(^OEORD(oeordId,"I",oeordSub,1),"^",2)
	.......s OperArcimDR=$G(^DHCOEOrdPrintSet("DeliverOrderArcimDr"))	
	.......i ArcimDR=OperArcimDR s OrdRedLine="Top"
 	......d OutPutTempOrder
    s qHandle=$lb(0,repid,0)
	q $$$OK
OutPutTempOrder
	s Data=$lb(OrderId,startDate,startTime,createDate,createTime,arcimDesc,doctor,excuteNurse,excuteDateTime,setDisconDate,setDisconTime,stopDoctor,stopNurse,createDateTime,starteDateTime,otherInfo,subChar,OrdRedLine)
 	s ^CacheTemp(repid,ind)=Data
 	s ind=ind+1
	q
	
getTakeOrdExTime  //取药医嘱执行时间取处理时间
	s priorDr=$P($G(^OEORD(oeordId,"I",oeordSub,1)),"^",8)   //医嘱优先级 
	q:priorDr=""
	s priorDesc=$P(^OECPR(priorDr),"^",2) 
	s priorDesc="^"_priorDesc_"^"	
	i priorDesc["取药"  d
	.s excuteInfo=##class(web.DHCLCNUREXCUTE).GetSeeOrdInfo(oeordId,oeordSub) 
 	.i excuteInfo'="^^" d
 	.q:$p(excuteInfo,"^",1)=""
 	.s excuteNurse=$p(^SSU("SSUSR",$p(excuteInfo,"^",1)),"^",2)
 	.s excuteDateTime=$zd($p(excuteInfo,"^",2),3)_" "_$zt($p(excuteInfo,"^",3),2)
 	q
}
```

### 修改长期医嘱单

```
ClassMethod FindLongOrderExecute(ByRef qHandle As %Binary, episodId As %String, locId As %String, startDate As %String, startTime As %String, endDate As %String, endTime As %String, reformTime As %String = "") As %Status
{
	s ^EH("FindXXXXOrder","Long")=$lb(episodId ,locId ,startDate ,startTime ,endDate ,endTime ,reformTime )
	s repid=$i(^CacheTemp)
 	s ind=1
 	i (episodId="")!(locId="") s qHandle=$lb(0,repid,0) q $$$OK
 	///日期和时间格式取配置
 	s dateType=3,timeType=2
 	s dateType = +$g(^DHCOEOrdPrintSet("dateType"))
 	s timeType = +$g(^DHCOEOrdPrintSet("timeType"))
 
 	s oeordId=$o(^OEORD(0,"Adm",episodId,""))
 	i oeordId="" s qHandle=$lb(0,repid,0) q $$$OK
 	s startDateTime=$zdh(startDate,3)*100000+$zth(startTime)
 	i endDate'="" s endDateTime=$zdh(endDate,3)*100000+$zth(endTime)
 	e  s endDateTime=""
 	s tmpReformTime=reformTime
 	i reformTime'="" d
 	.s reformDate=$zdh($p(reformTime," ",1),3)
 	.s reformTime=$zth($p(reformTime," ",2))
 	.s reformTime=reformDate*100000+reformTime
 	
	//s groupId = $g(^OrderSheetTemp(userID))
	//zhu ca
 	s CAFlag=##Class(web.DHCNurCASignVerify).GetIsVerifyCA(locId)
 	
 	
 	s oeordSub=0 f  s oeordSub=$o(^OEORD(oeordId,"I",oeordSub)) q:oeordSub=""  d
 	.q:$g(^OEORD(oeordId,"I",oeordSub,1))=""
 	.i (oeordId_"||"_oeordSub)=("2198||38") b
 	.i (oeordId_"||"_oeordSub)=("826||2") b
 	.s ordDep=$p(^OEORD(oeordId,"I",oeordSub,1),"^",3)     //下医嘱科室
 	.;q:ordDep'=locId
 	.s orderCreateDate=$p(^OEORD(oeordId,"I",oeordSub,3),"^",7)  //下医嘱日期
 	.s orderCreateTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",17)
 	.s orderInsertDate=$p(^OEORD(oeordId,"I",oeordSub,1),"^",19)  //插医嘱日期 儿童用
 	.s orderInsertTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",20)
 	.i (orderInsertDate'="")&&(orderInsertTime'="") d
 	..;s orderCreateTime=orderInsertTime
 	..;s orderCreateDate=orderInsertDate
 	.s orderCreateDateTime=orderCreateDate*100000+orderCreateTime
 	.q:orderCreateDateTime<startDateTime
 	.q:(endDateTime'="")&(orderCreateDateTime>endDateTime)
 	.s grassDrugFlag = ..ifGrassDrug(oeordId,oeordSub)
 	.;s prescName = ..getPrescName(oeordId,oeordSub)
 	.s prescSeqNo = $p(^OEORD(oeordId,"I",oeordSub,1),"^",15)
 	.q:(grassDrugFlag="Y")&&((+prescSeqNo)>1) ;&&(prescName'="")
 	.s prescSeqNO=$p(^OEORD(oeordId,"I",oeordSub,1),"^",14)
 	.s orderSttDate=$p(^OEORD(oeordId,"I",oeordSub,1),"^",9)  //医嘱开始日期
 	.s orderSttTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",10) 
 	.s priorDr=$P($G(^OEORD(oeordId,"I",oeordSub,1)),"^",8)   //医嘱优先级 
	.i priorDr'="" s priorDesc=$P(^OECPR(priorDr),"^",2)  
	.q:priorDr=""
	.s priorDesc="^"_priorDesc_"^"
	.s arcimDr=$P($G(^OEORD(oeordId,"I",oeordSub,1)),"^",2)
	.//ReformTimes不为空表示打印过
	.s ReformTimes=$o(^Nur.DoctorOrderPrintRecordSubI("OrderId"," "_oeordId_"||"_oeordSub,""))
	.//屏蔽医嘱状态
	.s ordStateDr=$P($G(^OEORD(oeordId,"I",oeordSub,1)),"^",13) //医嘱状态
	.q:((ordStateDr="2")!(ordStateDr=""))&(ReformTimes="")
	.//长期显示临时术后医嘱,为了分页  
	.s ArcimDR=$p(^OEORD(oeordId,"I",oeordSub,1),"^",2)
	.s OperArcimDR=$G(^DHCOEOrdPrintSet("OperOrderArcimDr"))
	.//长期显示临时产后医嘱,为了分页  
	.;s ArcimDeliverDR=$p(^OEORD(oeordId,"I",oeordSub,1),"^",2)
	.s DeliverArcimDr=$G(^DHCOEOrdPrintSet("DeliverOrderArcimDr"))
	.b:oeordSub=81 ;333
	.q:(^DHCOEOrdPrintSet("L","OrdTyp")'[priorDesc)&(ArcimDR'=OperArcimDR)&(ArcimDR'=DeliverArcimDr)&(ArcimDR'="18431||1")
	.//屏蔽科室
	.s schLoc=$P($G(^OEORD(oeordId,"I",oeordSub,7)),"^",2)
	.s schLoc=$p(schLoc,$c(1))
	.s schLoc="^"_$G(schLoc)_"^"
	.s lsNotLoc=$G(^DHCOEOrdPrintSet("NotLoc"))
	.q:($G(^DHCOEOrdPrintSet("NotLoc"))[schLoc)&((lsNotLoc'="^^")!(lsNotLoc'=""))&(ReformTimes="")
	.//屏蔽医护人员
	.s doctorDr=$P($G(^OEORD(oeordId,"I",oeordSub,1)),"^",11)
	.if doctorDr'="" s cpTypDR=$P($g(^CTPCP(doctorDr,1)),"^",4)  ;CTPCP_CarPrvTp_DR
	.q:$g(cpTypDR)=""
	.q:'$D(^CT("CPT",cpTypDR))
	.s cpTyp=""
	.i $G(cpTypDR)'="" s cpTyp=$P($g(^CT("CPT",cpTypDR)),"^",4)  ;CT_CarPrvTp
	.q:cpTyp'="DOCTOR"
 	.//按子类和大类屏蔽医嘱
 	.s itemCatDr=$P($G(^ARCIM(+arcimDr,$p(arcimDr,"||",2),1)),"^",10)
 	.s ordCatDr=$P(^ARC("IC",itemCatDr),"^",8)    
	.s ordCat=$P(^OEC("ORCAT",ordCatDr),"^",1)    
	.s ordCatDrStr="^"_ordCatDr_"^"
	.//大类
	.q:($G(^DHCOEOrdPrintSet("NotOrdCat"))[ordCatDrStr&($G(^DHCOEOrdPrintSet("NotOrdCat"))'=""))&(ReformTimes="")
	.s ItemCatDrStr="^"_itemCatDr_"^" 
	.//子类
	.q:($G(^DHCOEOrdPrintSet("NotSordCat"))[ItemCatDrStr&($G(^DHCOEOrdPrintSet("NotSordCat"))'=""))&(ReformTimes="")
 	.s seqNo=$P($G(^OEORD(oeordId,"I",oeordSub,3)),"^",4)  //医嘱序号
	.s relationMainOrd=$p($g(^OEORD(oeordId,"I",oeordSub,11)),"^",39) //关联主医嘱ID
    .i relationMainOrd'="" d
    ..s relationMainOrdSub=$P(relationMainOrd,"||",2) 
    .e  s relationMainOrdSub=oeordSub
    .s sortDate=$p(^OEORD(oeordId,"I",oeordSub,3),"^",7)  //下医嘱日期
 	.s sortTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",17)
    .s sortOrd(sortDate,sortTime,relationMainOrdSub,seqNo,oeordId,oeordSub)=""  //排序
    .s sortOrdId(oeordId,oeordSub)=""
    .;s ^pjf(orderSttDate,orderSttTime,relationMainOrdSub,seqNo,oeordId,oeordSub)="" 
    s orderSttDate="" f  s orderSttDate=$o(sortOrd(orderSttDate)) q:orderSttDate=""  d
    .s orderSttTime="" f  s orderSttTime=$o(sortOrd(orderSttDate,orderSttTime)) q:orderSttTime=""  d
    ..s relationMainOrdSub="" f  s relationMainOrdSub=$o(sortOrd(orderSttDate,orderSttTime,relationMainOrdSub)) q:relationMainOrdSub=""  d
    ...s seqNo="" f  s seqNo=$o(sortOrd(orderSttDate,orderSttTime,relationMainOrdSub,seqNo)) q:seqNo=""  d
    ....s oeordId="" f  s oeordId=$o(sortOrd(orderSttDate,orderSttTime,relationMainOrdSub,seqNo,oeordId)) q:oeordId=""  d
    .....s oeordSub="",stopDate=""
    .....f  s oeordSub=$o(sortOrd(orderSttDate,orderSttTime,relationMainOrdSub,seqNo,oeordId,oeordSub)) q:oeordSub=""  d
    ......//startDate:%String,startTime:%String,createDate:%String,createTime:%String,arcimDesc:%String,doctor:%String,excuteNurse:%String,excuteDateTime:%String,setDisconDate:%String,setDisconTime:%String,stopDoctor:%String,stopNurse:%String
    ......s startDate=$p(^OEORD(oeordId,"I",oeordSub,1),"^",9)  //医嘱开始日期
 	......s startTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",10) 
 	......s createDate=$p(^OEORD(oeordId,"I",oeordSub,3),"^",7)  //下医嘱日期
 	......s createTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",17)
 	......//医嘱名称
 	......i oeordSub=145 b
 	......s arcimDesc=..getOrderName(oeordId,oeordSub)
 	......i startDate'=createDate s arcimDesc=arcimDesc_"(执行日期:"_$zd(startDate,dateType)_")"
	......//下医嘱医生
 	......s addUserDr=$P($G(^OEORD(oeordId,"I",oeordSub,7)),"^",1)
	......q:addUserDr=""
    ......s doctor=$P($G(^SSU("SSUSR",addUserDr)),"^",2) // EH
    ......//zhu ca
    ......s:CAFlag doctor=doctor_"*"_addUserDr
    ......//实习医生
    ......s practiceDoctor=""
    ......s practiceAddUserDr=$P($G(^OEORD(oeordId,"I",oeordSub,"DHC")),"^",36)
    ......i practiceAddUserDr'="" s practiceDoctor=$P(^SSU("SSUSR",practiceAddUserDr),"^",2)
    ......i practiceDoctor'="" s doctor=practiceDoctor_"/"_doctor
    ......//审核医生
    ......s auditDoctor =..limitDrugAuditDoc(oeordId_"||"_oeordSub)
    ......s:auditDoctor'="" doctor=auditDoctor_"/"_doctor
    ......//医嘱开始日期
    ......s orderStartDate=$p(^OEORD(oeordId,"I",oeordSub,1),"^",9)  
 	......s orderStartTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",10)
 	......s startDate=$zd(orderStartDate,dateType)
 	......s startTime=$zt(orderStartTime,timeType)
 	......s starteDateTime=orderStartDate*100000+orderStartTime
 	......//医嘱创建日期
 	......s orderCreateDate=$p(^OEORD(oeordId,"I",oeordSub,3),"^",7)  
 	......s orderCreateTime=$p(^OEORD(oeordId,"I",oeordSub,1),"^",17)
 	......s createDate=$zd(orderCreateDate,dateType)
 	......s createTime=$zt(orderCreateTime,timeType)
 	......s createDateTime=orderCreateDate*100000+orderCreateTime
 	......//执行医嘱信息
 	......s excuteNurse="",excuteDateTime=""
 	......s excuteInfo=..getExcuteInfo(oeordId,oeordSub,reformTime)
 	......i excuteInfo'="^^" d
 	.......s excuteNurse=$p(^CTPCP($p(excuteInfo,"^",1),1),"^",2)
 	.......s excuteDateTime=$zd($p(excuteInfo,"^",2),3)_" "_$zt($p(excuteInfo,"^",3),2)
 	......s excuteInfo=##class(web.DHCLCNUREXCUTE).GetSeeOrdInfo(oeordId,oeordSub) 
 	......i excuteInfo'="^^" d
 	.......q:$p(excuteInfo,"^",1)=""
 	.......s excuteNurse=$p(^SSU("SSUSR",$p(excuteInfo,"^",1)),"^",2)
 	.......s excuteDateTime=$zd($p(excuteInfo,"^",2),3)_" "_$zt($p(excuteInfo,"^",3),2)
 	.......//zhu ca
 	.......s:CAFlag excuteNurse=excuteNurse_"*"_$p(excuteInfo,"^",1)
 	......//停止医嘱信息
 	......s disconInfo=##class(web.DHCLCNUREXCUTE).GetXOrdInfo(oeordId,oeordSub)
 	......i (disconInfo'="^^")&&($d(^SSU("SSUSR",$p(disconInfo,"^",1)))) d
 	.......s stopNurse=$p($g(^SSU("SSUSR",$p(disconInfo,"^",1))),"^",2)
 	.......// zhu ca
 	.......s:CAFlag stopNurse=stopNurse_"*"_$p(disconInfo,"^",1)
 	.......s setDisconDate=$zd($p(disconInfo,"^",2),3)
 	.......s setDisconTime=$zt($p(disconInfo,"^",3),2)
 	......e  s stopNurse="",setDisconDate="",setDisconTime=""
 	......s stopDoctorStr=..getStopDoctor(oeordId,oeordSub)
 	......s stopDoctor=$p(stopDoctorStr,"^",1)
 	......// zhu ca
 	......s:CAFlag stopDoctor=stopDoctor_"*"_..getStopDoctorId(oeordId,oeordSub)
 	......s stopDate=$p(stopDoctorStr,"^",2)
 	......;i $e(stopDate,1,4)=+$e($zd(+$h,3),1,4) s stopDate=$E(stopDate,6,10)	//跨年才显示年份
#; 	......i stopDate'="" d
#; 	.......s stopDate=$TR($E(stopDate,6,10),"-","/")
#; 	.......s stopDate=$p(stopDate,"/",1)_"月"_$p(stopDate,"/",2)_"日"
 	......s stopTime=$p(stopDoctorStr,"^",3)
 	......s OrderId=oeordId_"||"_oeordSub
 	......i (excuteNurse="")&(stopNurse'="") d
 	.......s excuteNurse=stopNurse
 	......//医嘱状态
 	......s otherInfo=""
 	......s ordStateDr=$P($G(^OEORD(oeordId,"I",oeordSub,1)),"^",13) //医嘱状态
	......s orderState=$p(^OEC("OSTAT",ordStateDr),"^",1)
	......i orderState="U" d
	.......s otherInfo="----DC"
	.......s excuteNurse="",excuteDateTime=""
	......i orderState="C" d
	.......s otherInfo="----Cancel" ;_..orderCancleInfo(oeordId,oeordSub)
	.......s excuteNurse="",excuteDateTime=""
	......s subChar=""
	......i $g(^DHCOEOrdPrintSet("ifShowOrderGroupFlag"))="true" s subChar=..getSubChar(oeordId, oeordSub,.sortOrdId)
	......s OrdRedLine=""  
	......i $g(^DHCOEOrdPrintSet("ifLongOperRedLine"))="true" d  //手术医嘱划线
	.......s ArcimDR=$p(^OEORD(oeordId,"I",oeordSub,1),"^",2)
	.......s OperArcimDR=$G(^DHCOEOrdPrintSet("OperOrderArcimDr"))	
	.......i ArcimDR=OperArcimDR s OrdRedLine="Top"
	......i $g(^DHCOEOrdPrintSet("ifLongDeliverRedLine"))="true" d  //产后医嘱划线
	.......s ArcimDR=$p(^OEORD(oeordId,"I",oeordSub,1),"^",2)
	.......s OperArcimDR=$G(^DHCOEOrdPrintSet("DeliverOrderArcimDr"))	
	.......i ArcimDR=OperArcimDR s OrdRedLine="Top"
 	......d OutPutLongOrder
    s qHandle=$lb(0,repid,0)
	q $$$OK
OutPutLongOrder
	s Data=$lb(OrderId,startDate,startTime,createDate,createTime,arcimDesc,doctor,excuteNurse,excuteDateTime,setDisconDate,setDisconTime,stopDoctor,stopNurse,createDateTime,starteDateTime,orderState,tmpReformTime,otherInfo,stopDate,stopTime,subChar,OrdRedLine)
 	s ^CacheTemp(repid,ind)=Data
 	s ind=ind+1
	q
}
```

## 远程滇东北


671009861
dhcc1234

库：10.10.16.178/imedical/web

## 医嘱单调试入口

P_0=150442&
P_1=54&
P_2=187&
P_3=11&
P_4=http%3A%2F%2F10.10.16.178&
P_5=true