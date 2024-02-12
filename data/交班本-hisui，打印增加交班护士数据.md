


```
ClassMethod GetShiftCommonData(dynamicParam)
{
	s ^GetShiftCommonData("dynamicParam")=dynamicParam
	s locID = $p(dynamicParam,"^",2)
	s wardID = $p(dynamicParam,"^",3)
	s ShiftDate = $p(dynamicParam,"^",4)
	
	s data = $g(^CTLOC(locID))
	s desc = $p(data,"^", 2)
	s hospID=$p(data,"^",22)
	s hospID=##class(Nur.NIS.Common.Utils).GetHospitalID("Nur_IP_ExchangeItem",hospID)
	s HospitalDesc=$P(^CT("HOSP",hospID),"^",2)
	
	///指定班次，交接班护士
	s ShiftDateL = ##Class(websys.Conversions).DateHtmlToLogical(ShiftDate)
	s shiftRid = ##class(Nur.SHIFT.Service.ShiftBizV2).GetMainDR(wardID,ShiftDateL)
	s shiftSid = "0" f{
		s shiftSid = $o(^Nur.SHIFT.ShiftRecord(shiftRid,"C",shiftSid))
		q:shiftSid=""
		s sort(shiftSid,"Nurse1")=""
		s sort(shiftSid,"Nurse2")=""
		s nurse = $p(^Nur.SHIFT.ShiftRecord(shiftRid,"C",shiftSid),"^",2)
		i nurse'=""{
			s nurseNameStr = ""
			f i=1:1:$l(nurse,"@"){
				s careId = $p(nurse,"@",i)
				continue:careId=""
				s userId = $o(^SSU("SSUSR",0,"CTPCP",careId,""))
				s nurseName = $p(^SSU("SSUSR",userId),"^",2)
				s nurseNameStr = $s(nurseNameStr="":nurseName,1:nurseNameStr_" "_nurseName)
			}
			s sort(shiftSid,"Nurse1") = nurseNameStr
		} 
		s nurse = $p(^Nur.SHIFT.ShiftRecord(shiftRid,"C",shiftSid),"^",4)
		i nurse'=""{
			s nurseNameStr = ""
			f i=1:1:$l(nurse,"@"){
				s careId = $p(nurse,"@",i)
				continue:careId=""
				s userId = $o(^SSU("SSUSR",0,"CTPCP",careId,""))
				s nurseName = $p(^SSU("SSUSR",userId),"^",2)
				s nurseNameStr = $s(nurseNameStr="":nurseName,1:nurseNameStr_" "_nurseName)
			}
			s sort(shiftSid,"Nurse2") = nurseNameStr
		}
	}
	s shiftNurseRet = ""
	s shiftSid = "" f{
		s shiftSid = $o(sort(shiftSid))
		q:shiftSid=""
		s nurse1 = sort(shiftSid,"Nurse1")
		s nurse2 = sort(shiftSid,"Nurse2")
		s ret = shiftSid_"Nurse1@"_nurse1
		s ret = ret_"^"_shiftSid_"Nurse2@"_nurse2
		s shiftNurseRet=$s(shiftNurseRet="":ret,1:shiftNurseRet_"^"_ret)
	}
	s 
	// shiftNurseRet="1Nurse1@nurse 梁珠珠^1Nurse2@梁珠珠^2Nurse1@梁珠珠^2Nurse2@nurse innurse^3Nurse1@^3Nurse2@"
	q shiftNurseRet_"^"_"ShiftLocDesc@"_desc_"^ShiftDate@"_ShiftDate_"^HospitalDesc@"_HospitalDesc
}
```