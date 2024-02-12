```
		onCheck:function(node,checked){
			if (typeof node.ID !== 'number') {
				checkAllChildNodes(node, checked);
			}
			getAllCheckedPatient();
			if ((typeof(patientTreeCheckChangeHandle) === 'function')) {
				patientTreeCheckChangeHandle();
			}	
		}
```