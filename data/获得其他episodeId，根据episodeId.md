```js
ClassMethod getOtherEpisodeIdStrByEpisodeId(episodeId)
{
    s result=""
    s personId = +^PAADM(episodeId)
    s otherEpisodeId=""
    f{
        s otherEpisodeId=$o(^PAPERdr(personId,"ADM","I",otherEpisodeId))
        q:otherEpisodeId=""
        i result="" s result=otherEpisodeId
        e  s result=result_"^"_otherEpisodeId
    }
    q result
}
```