---
title: Home
sections:
- type: heroblock
  template: heroblock
  section_id: hero
  component: hero_block.html
  content: This is my portfolio.
  title: Hi, I'm Ashli
  actions: []
- type: contentblock
  template: contentblock
  title: About Me
  section_id: about
  actions:
  - label: Contact
    url: "/contact"
  component: content_block.html
  content: I was first introduced to 'big data' working as an engineer at a 24/7 semiconductor
    fabrication facility, and have been honing my skills on how to explore it, visualize
    it, and build a narrative with it ever since. Some of the fields and areas that
    I have studied to facilitate this venture include Time Series Analysis, Deterministic
    Optimization, Machine Learning (supervised and unsupervised), Bayesian Statistics,
    and cloud computing on AWS and Databricks. I have developed a fluency in R, Python,
    SQL, Pig and Scala, and use Tableau and D3.JS to share the compelling insights
    that I gain from my data exploration. Check out my website and profile to see
    more of what I have learned.
  image: ''
- type: postsblock
  template: postsblock
  title: Featured Projects
  section_id: recent-posts
  actions:
  - label: View more projects
    url: blog/index.html
  component: posts_block.html
  num_posts_displayed: 4
layout: home
menu:
  main:
    weight: 1

---
