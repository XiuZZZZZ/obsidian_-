

## 业务逻辑

- 护理计划-hisui
	- 逻辑
		- 页面布局
			- 护理问题id  NurQuestionTable
				- 问题初始化  InitNurQuestionTableGrid();
				- 问题查询  
			- 护理目标id  
				- 目标查询：Nur.NIS.Service.NursingPlan.GoalInterventionRecord  QueryGoalByQestID
			- 护理措施id  QuestionMeasureTable
				- 措施查询：Nur.NIS.Service.NursingPlan.GoalInterventionRecord    QueryInterventionByQestID
	- 逻辑单元
		- csp：nur.hisui.nurseplanmake.csp  nur.hisui.nurseplanmake.show.csp
		- js：../scripts/nurse/hisui/NursePlanMake.js
		- 提交措施生成护嘱托：Nur.NIS.Service.NursingPlan.GoalInterventionRecord  SaveIntervRecord  有频次的时候才生成
		- 护理问题id  NurQuestionTable
		- 问题初始化  InitNurQuestionTableGrid();
		- 问题查询  
		- 护理目标id  
		- 目标查询：Nur.NIS.Service.NursingPlan.GoalInterventionRecord  QueryGoalByQestID
		- 护理措施id  QuestionMeasureTable
		- 措施查询：Nur.NIS.Service.NursingPlan.GoalInterventionRecord    QueryInterventionByQestID


## 常见问题

- 已经停了护理病历的配置但是还是带出来了
	- [[已经停了的，但是还是带出来了问题.canvas]]