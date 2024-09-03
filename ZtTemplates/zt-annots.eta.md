<% const byColor = Object.groupBy(it, (annot) => annot.colorName);
const label = {
  "red": "先前研究的问题",
  "orange": "代研究点",
  "yellow": "创新点",
  "gray": "疑惑点",
  "green": "性能成绩",
  "cyan": "青色",
  "blue": "技术细节",
  "navy": "海军蓝",
  "purple": "紫色",
  "brown": "棕色",
  "magenta": "品红",
};
// 合并有标签的颜色和任何未指定标签的颜色
// 保持在label中设置的颜色顺序
const colorSet = new Set([...Object.keys(label), ...Object.keys(byColor)]);
for (const color of colorSet) { 
if (!(color in byColor)) continue -%>

### <%= label[color] ?? color %>
  <%_ for (const annot of byColor[color]) { %>

<%~ include("annotation", annot) %>
  <%_ } %>
<% } %>