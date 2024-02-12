
```
			<csp:if condition='firstAssignBedFlag="1"'>
				<div style="height:calc(100% - 2px);border:1px solid #ccc;border-radius:4px;">
					<table class="search-table firstAssignBedTable" style="margin:0 auto;width:100%;">
						<tr>
							<td class="titleInfo" colspan="4" style="text-align:center;">
								<span class='first-separateBeds active'>#(..Get("首次分床"))#</span>
								<span style='margin:0 10px;'>#(..Get("将"))#</span>
								<server>
									w "<span class='bedNo'>"_patientName_"</span>"
								</server>
								<span style='margin:0 10px;'>#(..Get("分配至"))#</span>
							    <server>
									w "<span class='bedNo'>"_bedNo_"</span>"
								</server>
							</td>
						</tr>
						<tr>
							<td class="r-label1">
								<label for='date' class='clsRequired'>#(..Get("日期"))#</label>
							</td>
							<td>
								<input id="date" class="hisui-datebox textbox" disabled>
							</td>
							<td class="r-label1">
								<label for='time' class='clsRequired'>#(..Get("时间"))#</label>
							</td>
							<td>
								<input id="time" class="hisui-timespinner textbox" data-options="showSeconds:true">
							</td>
						</tr>
						<tr>
							<td class="r-label1">
								<label for='docList'>#(..Get("主管医生"))#</label>
							</td>
							<td>
								<input id="docList" class="hisui-combobox textbox">
							</td>
							<td class="r-label1">
								<label for='practiceDocList'>#(..Get("实习医生"))#</label>
							</td>
							<td>
								<input id="practiceDocList" class="hisui-combobox textbox">
							</td>
							<td class="r-label1">
								<label for='nurList'>#(..Get("主管护士"))#</label>
							</td>
							<td>
								<input id="nurList" class="hisui-combobox textbox">
							</td>
						</tr>
						<csp:if condition='docUnitDisplay="Y"'>
							<tr>
								<td class="r-label1">
									<label for='docUintList'>#(..Get("医疗组"))#</label>
								</td>
								<td>
									<input id="docUintList" class="hisui-combobox textbox">
								</td>
							</tr>
						</csp:if>
						<tr>
							<td  colspan="4" style="font-size:0;text-align:center;">
								<a class="hisui-linkbutton" id="BSave">保存</a>
								<a class="hisui-linkbutton" id="BCancel" style="margin-left:10px;">取消</a>
							<td>
						</tr>
					</table>
				</div>
			<csp:else>
				<div style="height:calc(100% - 2px);border:1px solid #ccc;border-radius:4px;">
					<table class="search-table" style="margin:calc(100% - 218px) auto auto auto;">
						<tr style="display:none;">
							<td class="r-label1">
								<label for='date' class='clsRequired'>#(..Get("日期"))#</label>
							</td>
							<td>
								<input id="date" class="hisui-datebox textbox" disabled>
							</td>
							<td class="r-label1">
								<label for='time' class='clsRequired'></label>
							</td>
							<td>
								<input id="time" class="hisui-timespinner textbox" data-options="showSeconds:true">
							</td>
						</tr>
						<tr>
							<td class="titleInfo" colspan="2" style="text-align:center;">
								<span style='margin:0 10px;'>#(..Get("将"))#</span>
								<server>
									w "<span class='bedNo'>"_patientName_"</span>"
								</server>
									<span style='margin:0 10px;'>#(..Get("分配至"))#</span>
								 <server>
									w "<span class='bedNo'>"_bedNo_"</span>"
								</server>
							</td>
						</tr>
						<tr>
							<!--td class="r-label1">
								<label for='docList'>#(..Get("主管医生"))#</label>
							</td>
							<td>
								<input id="docList" class="hisui-combobox textbox">
							</td-->
							<td colspan="2" style="font-size:0;text-align:center;">
								<label for='docList' style="padding-right:10px;vertical-align:middle;">#(..Get("主管医生"))#</label>
								<input id="docList" class="hisui-combobox textbox">
							</td>
						</tr>
						<tr>
							<td colspan="2" style="font-size:0;text-align:center;">
								<label for='practiceDocList' style="padding-left:8px;padding-right:10px;vertical-align:middle;">#(..Get("实习医生"))#</label>
								<input id="practiceDocList" class="hisui-combobox textbox">
							</td>
						</tr>
						<csp:if condition='docUnitDisplay="Y"'>
							<tr>
								<td colspan="2" style="font-size:0;text-align:center;">
									<label for='docUintList' style="padding-right:10px;vertical-align:middle;">#(..Get("医疗组"))#</label>
									<input id="docUintList" class="hisui-combobox textbox">
								</td>
							</tr>
						</csp:if>
						<tr>
							<!--td class="r-label1">
								<label for='nurList'>#(..Get("主管护士"))#</label>
							</td>
							<td>
								<input id="nurList" class="hisui-combobox textbox">
							</td-->
							<td colspan="2" style="font-size:0;text-align:center;">
								<label for='nurList' style="padding-right:10px;vertical-align:middle;">#(..Get("主管护士"))#</label>
								<input id="nurList" class="hisui-combobox textbox">
							</td>
						</tr>
						<tr>
							<td colspan="2" style="font-size:0;text-align:center;">
								<a class="hisui-linkbutton" id="BSave">保存</a>
								<a class="hisui-linkbutton" id="BCancel" style="margin-left:10px;">取消</a>
							<td>
						</tr>
					</table>
				</div>
			</csp:if>
```