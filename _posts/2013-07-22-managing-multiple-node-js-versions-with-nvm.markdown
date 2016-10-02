---
title: Managing Multiple Node.js Versions With NVM
date: '2013-07-22 10:18:47 +0300'
date_gmt: '2013-07-22 10:18:47 +0300'
categories:
- Node.js
tags:
- node.js
- nvm
- rvm
- version management

---
<p>If you are coming from a Ruby background or at least have some experience with it. The possibility that you are already familiar with multiple version management is pretty high (yes, talking about RVM).</p>
<p>Since Node.js is in very active development and getting releases pretty fast sometimes you may want to check if the version update breaks your dependencies or your application. But without having a version management tool for your Node.js installation it's pretty messy to upgrade and the vice versa. To avoid this and manage multiple different versions of Node.js installations NVM ( Node Version Manager) comes very handy.</p>
<h1>Installation</h1>
<p>Installing NVM is pretty simple ( assuming that you are on a POSIX system like OS X / Linux ) . Also you need a working C++ compiler.</p>
<pre> curl https://raw.github.com/creationix/nvm/master/install.sh | sh</pre>
<p>There you go now NVM is installed in your <strong>~/.nvm</strong> folder. If you are using Bash shell the NVM command will be available upon restarting your Terminal.<br />
But if you are using a different shell like <strong>Zsh</strong> or so be sure to insert the NVM path to your <strong>./.zprofile</strong> file.</p>
<h2>Usage</h2>
<p>The usage is also pretty simple.</p>
<p>For example if you want to download,compile and install v0.11.3.</p>
<pre>nvm install 0.11.3</pre>
<p>To use the newly installed version.</p>
<pre>nvm use 0.11.3</pre>
<p>To see your current Node installations.</p>
<pre>nvm ls</pre>
<p>To see the available Node installations.</p>
<pre>nvm ls-remote</pre>
<p>And to make a version the default in your system.</p>
<pre>nvm alias default 0.11.3</pre>
