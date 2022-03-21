---
title: About
description: "Alireza Samar is a computer scientist and machine learning researcher."  
nav: custom
custom-nav: 
    - '<a href="/now" title="Now">Now</a>&nbsp;|&nbsp;'
    - '<a href="/contact" title="Contact">Contact</a>'
permalink: /about
--- 

<figure class="about-picture"><img src="" alt="Alireza Samar with a friendly smile" title="Alireza Samar" id="aboutImg">
</figure>

<noscript>
<figure class="about-picture"><img src="/static/about-pictures/takava.jpeg" alt="Alireza Samar with a friendly smile" title="Alireza Samar"></figure>
</noscript>

I am migrating my contents now to this new website, will update here for sure!

I respond to emails very quickly, [feel free to write to me][con]. 

I used to have an email newsletter, but much prefer RSS anyway. [Subscribe to my feed][fed]. 

My relationship to social media is meh. [LinkedIn][lin] for professional stuff, [Twitter][twt] for something that I haven’t figured out yet or to share new preprint. 

<script>

// photo changer

const photos = [
"/static/about-pictures/alireza-samar.jpg"
]

const selectedPhoto = Math.floor(Math.random() * photos.length)

document.getElementById("aboutImg").setAttribute("src", photos[selectedPhoto]);

// copy email to clipboard

function copyEmailtoClipboard() {
    navigator.clipboard.writeText((arguments[0]));
}

</script>

[con]: /contact
[fed]: /feed.xml
[lin]: https://www.linkedin.com/in/alirezasamar
[twt]: https://twitter.com/alirezasamar