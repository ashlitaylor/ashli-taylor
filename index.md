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
  content: This is the "about" excerpt. TBD
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
