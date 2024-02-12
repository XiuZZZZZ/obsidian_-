

```
<script language="cache" runat="SERVER">
	s maternalBabyWadSub = ""
</script>

 <csp:if condition=((maternalBabyWadSub'="")&&(ifBaby=""))>
	<div data-options="region:'north',split:true" class="west_north_search" style="height:108px;">
		<input id="wardPatientSearchBox" class="hisui-searchbox" data-options="prompt:'#(..Get("姓名、登记号、床号"))#'" style="width:121px;"/>
		<div id="wardPatientCondition" class="hisui-switchbox" 
			 data-options="onText:'#(..Get("全部"))#',offText:'#(..Get("责组"))#',size:'small',animated:true,onClass:'primary',offClass:'gray'">
		</div>
		<div id="maternalBabyCondition" class="hisui-switchbox" 
			 data-options="onText:'#(..Get("母婴组合显示"))#',offText:'#(..Get("母婴拆分显示"))#',size:'small',animated:true,onClass:'primary',offClass:'gray'">
		</div>
		<div style="margin-top:5px;">
			<input id="patSignOrIconFilter" class="hisui-combobox"/>  
		</div>
	</div>
 <csp:else>
	<div data-options="region:'north',split:true" class="west_north_search" style="height:77px;">
		<input id="wardPatientSearchBox" class="hisui-searchbox" data-options="prompt:'#(..Get("姓名、登记号、床号"))#'" style="width:121px;"/>
		<div id="wardPatientCondition" class="hisui-switchbox" 
			 data-options="onText:'#(..Get("全部"))#',offText:'#(..Get("责组"))#',size:'small',animated:true,onClass:'primary',offClass:'gray'">
		</div>
		<div style="margin-top:5px;">
			<input id="patSignOrIconFilter" class="hisui-combobox"/>  
		</div>
	</div> 
 </csp:if>
```