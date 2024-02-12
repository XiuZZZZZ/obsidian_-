
放csp
```
<script language="cache" runat="SERVER">
	s EpisodeID = $G(%request.Data("EpisodeID", 1))
</script>



```

放body最后或者外面
```
	<script language="javascript">
		var EpisodeID = "#(EpisodeID)#";
	</script>
s EpisodeID = $G(%request.Data("EpisodeID", 1))
```