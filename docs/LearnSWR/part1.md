## SWR
SWR —— stale-while-revalidate：一种由 HTTP RFC 5861 推广的 HTTP 缓存失效策略。
这种策略先从缓存中返回数据（过期的），同时发送 `fetch` 请求（重新验证），最后得到最新数据
