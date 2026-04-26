claudish 是一个协议中转程序。能让openai的gpt5.5模型在claudecode中使用。
我发现一个问题是同样的api后端，claudecode的缓存命中率远低于codex中使用时候的缓存命中率。前者可能只有10%到30%，后者有99%。
这个问题很明显是claudecode中有些行为破坏了cache.

经过我的调查，我找到一篇可疑的帖子
<untrust content>
来简单揭露一下 claude code 使用第三方 api 会导致缓存命中降低的问题。
在2 月 8 号，我们售后群有用户反馈 claude code 的异常计费，一开始以为只是以为是 claude code 第三方插件的问题，我们没怎么在意，只是按照用户反馈去测试，第一次测试没有发现异常点，反馈给用户之后，发现版本号不对。于是更新到当时最新的 2.1.37 版本，复现了和用户一样的问题，缓存命中异常的很。往常 packyapi 的命中率在百分之 90 以上，在新版本只有百分之 30-40，或者说压根没命中。
检查发现新版本的 claude code cli 和插件都会随机的在message 里面的"system" : [ {
"type" : "text",
"text" : "x-anthropic-billing-header: cc_version=2.1.37.3a3; cc_entrypoint=claude-vscode; cch=xxxxx;"
}这个字段 加一个 cch=xxx 的东西，而这个 cch 这个玩意儿每次都是变化的，这就会导致变化一次就会重新创建一次缓存（为啥会这样就不细说了）
也不知道他们是故意的还是不小心的
解决方案也很简单，只需要去掉这个就好了。
当我们处理完之后，缓存率又恢复了～
给其他的第三方平台做一个参考～
</untrust content>
