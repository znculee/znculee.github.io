---
layout: default
title: Home
---

<head>

  <meta name="viewport" content="width=device-width, initial-scale=1">

  <style>
    img:hover {
      animation: shake 0.5s;
      animation-iteration-count: 1;
    }
    @keyframes shake {
      0% { transform: translate(1px, 1px) rotate(0deg); }
      10% { transform: translate(-1px, -2px) rotate(-1deg); }
      20% { transform: translate(-3px, 0px) rotate(1deg); }
      30% { transform: translate(3px, 2px) rotate(0deg); }
      40% { transform: translate(1px, -1px) rotate(1deg); }
      50% { transform: translate(-1px, 2px) rotate(-1deg); }
      60% { transform: translate(-3px, 1px) rotate(0deg); }
      70% { transform: translate(3px, 1px) rotate(-1deg); }
      80% { transform: translate(-1px, -1px) rotate(1deg); }
      90% { transform: translate(1px, 2px) rotate(0deg); }
      100% { transform: translate(1px, -2px) rotate(-1deg); }
    }
    icon a{display:inline-block;}
    icon img{
      margin-left: 5px;
      margin-right: 5px;
      width: 30px
    }
  </style>

  <script src="{{ site.baseurl }}/public/js/tabs.js" defer></script>
  <link href="{{ site.baseurl }}/public/css/tabs.css" rel="stylesheet" />

</head>

<div style="float:right;">
  <img src="{{ site.baseurl }}/public/img/avatar.png" width="200" />
</div>

<p>
  Hi, I'm Xintong Li (李昕同), a machine learning research engineer in <a href="https://www.apple.com">Apple</a> at <a href="https://goo.gl/maps/Pk3NpxiNMsvdJp4K9">Cupertino</a>, working on machine translation.
  I am primaly interested in better understanding human languages and developing translation technology that enables connecting people across language barriers.
  I'm also excited about machine learning related topics.
</p>

<icon>
  <a href="https://github.com/znculee"><img alt="Github" src="{{ site.baseurl }}/public/img/github_icon.svg" /></a>
  <a href="https://twitter.com/znculee"><img alt="Twitter" src="{{ site.baseurl }}/public/img/twitter_icon.svg" /></a>
  <a href="https://scholar.google.com/citations?user=l996-A8AAAAJ"><img alt="Google Scholar" src="{{ site.baseurl }}/public/img/google_scholar_icon.svg" /></a>
  <a href="https://znculee.github.io/cv/"><img alt="CV" src="{{ site.baseurl }}/public/img/cv_icon.svg" /></a>
  <a href="mailto:znculee@gmail.com"><img alt="Gmail" src="{{ site.baseurl }}/public/img/gmail_icon.svg" /></a>
  <a href="https://t.me/znculee"><img alt="Telegram" src="{{ site.baseurl }}/public/img/telegram_icon.svg" /></a>
  <a href="https://www.instagram.com/xintong.znculee"><img alt="Instagram" src="{{ site.baseurl }}/public/img/instagram_icon.svg" /></a>
</icon>

<ul class="tabs">
  <li data-tab-target="#news" class="active tab">News</li>
  <li data-tab-target="#research" class="tab">Research</li>
  <li data-tab-target="#bio" class="tab">Bio</li>
  <li data-tab-target="#software" class="tab">Software</li>
  <!--<li data-tab-target="#misc" class="tab">Misc</li>-->
  <li data-tab-target="#contact" class="tab">Contact</li>
</ul>

<div class="tab-content">

  <div id="news" data-tab-content class="active">
    <ul>
      <li> 2025/09: Apple announced live translation with AirPods Pro 3. [<a href="https://www.apple.com/newsroom/2025/09/new-apple-intelligence-features-are-available-today/">news</a>] </li>
      <li> 2025/06: Apple announced live translation for Messages, FaceTime, and Phone Apps. [<a href="https://www.apple.com/newsroom/2025/06/apple-intelligence-gets-even-more-powerful-with-new-capabilities-across-apple-devices/">news</a>] </li>
      <li> 2022/07: One paper won best demo award at NAACL. [<a href="https://2022.naacl.org/blog/best-demo-award/">award</a>] </li>
      <li> 2022/05: One paper was accepted by ICML. [<a href="https://arxiv.org/abs/2203.09690">arxiv</a>] </li>
      <li> 2022/04: One paper was accepted by NAACL Demo Track. [<a href="https://github.com/PaddlePaddle/PaddleSpeech">github</a>] </li>
      <li> 2021/08: One paper was accepted by EMNLP. [<a href="https://aclanthology.org/2021.emnlp-main.53/">paper</a>] </li>
      <li> 2021/07: Two papers were accepted by INLG. [<a href="https://twitter.com/mwhite14850/status/1439775581357461504">twitter</a>]</li>
    </ul>
  </div>

  <div id="research" data-tab-content>
    <p>
      Listing of my papers on <a href="https://scholar.google.com/citations?user=l996-A8AAAAJ">Google Scholar</a>, a subset in <a href="https://aclanthology.org/people/x/xintong-li/">ACL Anthology</a>.
    </p>
  </div>

  <div id="bio" data-tab-content>
    <p>
      I am from <a href="https://goo.gl/maps/HFK7JpXNL5wqN8sU7">Shenyang</a>.
      I earned my PhD degree on machine translation in the <a href="http://www.ee.cuhk.edu.hk/en-gb/">Department of Electronic Engineering</a> at <a href="http://www.cuhk.edu.hk/english/index.html">The Chinese University of Hong Kong</a> in July 2019, advised by Prof. <a href="https://www.ee.cuhk.edu.hk/~qhmeng/">Max Q.-H. Meng</a>.
      Meanwhile, I was a research intern at <a href="https://ai.tencent.com/ailab/en/index">Tencent AI Lab</a> advised by Dr. <a href="https://lemaoliu.github.io/">Lemao Liu</a>.
      After graduation, I joined the <a href="https://linguistics.osu.edu/">Department of Linguistics</a> at <a href="https://www.osu.edu/">The Ohio State University</a> as a postdoc, working on natural language generation with Prof. <a href="https://u.osu.edu/white.1240/">Michael White</a>.
      Then, I was a research scientist in <a href="http://research.baidu.com/">Baidu Research</a> at <a href="https://goo.gl/maps/DjBF24yo4RexdpjN7">Sunnyvale</a>, working on simultaneous translation with Prof. <a href="http://web.engr.oregonstate.edu/~huanlian/">Liang Huang</a>.
      Now, I'm continuing my journey at <a href="https://www.apple.com">Apple</a>...
    </p>
  </div>

  <div id="software" data-tab-content>
    <ul>
      <li><a href="https://github.com/znculee/align-label-tool">word alignment labeling tool</a></li>
      <p>A commend line tool for viewing or labeling word alignments of parallel sentences.</p>
    </ul>
  </div>

  <!--<div id="misc" data-tab-content>-->
    <!--<p>-->
    <!--</p>-->
  <!--</div>-->

  <div id="contact" data-tab-content>
    <address>
      znculee AT gmail DOT com<br />
      One Apple Park Way, Cupertino, CA 95014
    </address>
  </div>

</div>
