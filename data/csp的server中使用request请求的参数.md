```javascript
<script language="cache" runat="SERVER">
	s PatientListPage=""
	s EpisodeID = $G(%request.Data("EpisodeID", 1))
	s OutWindowFlag=$G(%request.Data("OutWindowFlag", 1))
	s SinglePatient = $G(%request.Data("SinglePatient", 1))
	s IsPatient=%request.Get("IsPatient")
</script>

<script>
var EpisodeID = "#(EpisodeID)#";
</script>
```