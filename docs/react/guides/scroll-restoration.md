---
id: scroll-restoration
title: 滚动恢复 - Scroll Restoration
---

Traditionally, when you navigate to a previously visited page on a web browser, you would find that the page would be scrolled to the exact position where you were before you navigated away from that page. This is called **scroll restoration** and has been in a bit of a regression since web applications have started moving towards client side data fetching. With TanStack Query however, that's no longer the case.

传统上，当您在网络浏览器中导航到以前访问过的页面时，您会发现该页面将滚动到您离开该页面之前的确切位置。这被称为**滚动恢复（scroll restoration）**，自从Web应用程序开始向客户端数据获取移动以来，它有点退化了。然而，使用 TanStack Query 就不再是这种情况了。

Out of the box, "scroll restoration" for all queries (including paginated and infinite queries) Just Works™️ in TanStack Query. The reason for this is that query results are cached and able to be retrieved synchronously when a query is rendered. As long as your queries are being cached long enough (the default time is 5 minutes) and have not been garbage collected, scroll restoration will work out of the box all the time.

在 TanStack Query 中，所有查询（包括分页和无限查询）都默认支持"滚动恢复"。原因是查询结果被缓存，并且在渲染查询时可以同步检索。只要您的查询被缓存的时间足够长（默认为5分钟）并且没有被垃圾回收，滚动恢复将始终正常工作。