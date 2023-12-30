# <%= it.title %>

**文章链接**: [Zotero](<%= it.backlink %>) <%= it.fileLink %>
**网页链接**: [URL](<%= it.url %>)
## Abstract

>[!abstract]
><%= it.abstractNote %>

## Comments
<%~ include("annots", it.annotations) %>
