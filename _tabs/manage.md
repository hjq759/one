---
layout: page
title: 文章管理
icon: fas fa-pen
order: 5
---
<p>在这里准备新文章，或选择已有文章进行修改、删除。操作会跳转到 GitHub，由仓库主人登录后提交确认。提交后网站自动更新。</p>
<form id="compose">
<label for="post-title">文章标题</label>
<input id="post-title" required maxlength="150" style="display:block;width:100%;margin:8px 0 16px;padding:10px" placeholder="给文章起个标题">
<label for="post-category">分类</label>
<input id="post-category" maxlength="80" style="display:block;width:100%;margin:8px 0 16px;padding:10px" value="随笔">
<label for="post-body">正文（支持 Markdown）</label>
<textarea id="post-body" required rows="14" style="display:block;width:100%;margin:8px 0 16px;padding:10px" placeholder="开始写作……"></textarea>
<button type="submit" class="btn btn-primary">前往 GitHub 提交新文章</button>
<p id="compose-status" role="status" style="margin-top:12px"></p>
</form>
<p>填写内容会暂存在当前浏览器。跳转后，在 GitHub 点击“Commit changes（提交更改）”才会发布。请勿在文章中填写密码等私密信息。</p>
<h2>已有文章</h2>
<ul>
{% for post in site.posts %}
<li style="margin-bottom:18px"><a href="{{ post.url | relative_url }}">{{ post.title | escape }}</a><br>
<a href="https://github.com/hjq759/one/edit/main/{{ post.path | uri_escape }}">修改文章</a>
 · <a href="https://github.com/hjq759/one/delete/main/{{ post.path | uri_escape }}">删除文章</a></li>
{% endfor %}
</ul>
<p>删除链接会打开 GitHub 的删除确认页，提交后才会删除。访客无法修改你的仓库。</p>
<script>
(function(){
const title=document.getElementById('post-title'),category=document.getElementById('post-category'),body=document.getElementById('post-body'),status=document.getElementById('compose-status');
const key='hjq-one-compose-v1';
try{const saved=JSON.parse(localStorage.getItem(key)||'null');if(saved){title.value=saved.title||'';category.value=saved.category||'随笔';body.value=saved.body||'';}}catch(e){}
for(const el of [title,category,body])el.addEventListener('input',()=>{try{localStorage.setItem(key,JSON.stringify({title:title.value,category:category.value,body:body.value}));status.textContent='草稿已保存在当前浏览器';}catch(e){status.textContent='无法保存本地草稿，请复制正文备份';}});
document.getElementById('compose').addEventListener('submit',function(event){
event.preventDefault();
if(!title.value.trim()||!body.value.trim()){status.textContent='请填写标题和正文';return;}
const now=new Date();
const date=new Intl.DateTimeFormat('en-CA',{timeZone:'Asia/Shanghai',year:'numeric',month:'2-digit',day:'2-digit'}).format(now);
const slug='post-'+now.getTime();
const text='---\ntitle: '+JSON.stringify(title.value.trim())+'\ndate: '+date+' 00:00:00 +0800\ncategories: ['+JSON.stringify(category.value.trim()||'随笔')+']\n---\n\n'+body.value;
const url=new URL('https://github.com/hjq759/one/new/main/_posts');
url.searchParams.set('filename',date+'-'+slug+'.md');url.searchParams.set('value',text);
if(url.href.length>7500){status.textContent='文章较长，请在 GitHub 新建文件后粘贴正文。你的草稿仍保存在此页面。';return;}
window.location.assign(url.href);
});
})();
</script>
