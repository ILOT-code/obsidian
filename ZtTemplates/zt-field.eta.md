title: "<%= it.title %>"
authors: [<%= it.authors %>]
doi: <%= it.DOI %>
conference: xxx
citekey: "<%= it.citekey %>"
tags: [<%= it.tags.filter(t => t.type === 0) %>]