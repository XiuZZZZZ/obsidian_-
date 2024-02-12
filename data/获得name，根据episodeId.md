```
s personId=$p(^PAADM(episodeId),"^",1)  
s personName=$p(^PAPER(personId,"ALL"),"^",1)
```