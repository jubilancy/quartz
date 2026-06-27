---
title: "hugo widgets"
tags: code
---

# widgetzzzz

{{ partial "widgets/music-widget.html" . }}  
{{ partial "widgets/todo-widget.html" . }}  
{{ partial "widgets/scroll-progress.html" . }}  
{{ partial "widgets/dark-mode-toggle.html" . }}  
{{ partial "widgets/lazy-loading-gallery.html" . }}  
{{ partial "widgets/tag-cloud.html" . }}  
{{ partial "widgets/random-quote-generator.html" . }}  
{{ partial "widgets/custom-search.html" . }}  
{{ partial "widgets/weather.html" . }}  
{{ partial "widgets/countdown.html" . }}  
{{ partial "widgets/animated-background.html" . }}  
  
  
  
Here are code snippets for the requested fun interactive widgets and features, ready for integration as partials or directly in your pages:

## Dynamic Last.fm Music Widget

A widget showing your current or recent track using Last.fm API, auto-refreshing every 60 seconds.

```
xml<!-- Music Widget Partial (Last.fm) -->
<div id="music-widget">Loading current track...</div>
<script>
  const user = 'your_lastfm_username';
  const apiKey = 'your_lastfm_api_key';
  async function fetchCurrentTrack() {
    const url = `https://ws.audioscrobbler.com/2.0/?method=user.getrecenttracks&user=${user}&api_key=${apiKey}&format=json&limit=1`;
    const res = await fetch(url);
    const data = await res.json();
    if (data.recenttracks && data.recenttracks.track && data.recenttracks.track.length > 0) {
      const track = data.recenttracks.track[0];
      document.getElementById('music-widget').innerHTML =
        `<a href="${track.url}" target="_blank">${track.artist['#text']} - ${track.name}</a>`;
    } else {
      document.getElementById('music-widget').innerText = 'No recent tracks found.';
    }
  }
  fetchCurrentTrack();
  setInterval(fetchCurrentTrack, 60000); // refresh every 60 seconds
</script>
```

## Interactive To-Do List Widget

An editable to-do list or notes widget with localStorage persistence.

```
xml<!-- To-Do List Widget -->
<div id="todo-widget">
  <h3>To-Do List</h3>
  <input type="text" id="new-task-input" placeholder="Add a new task" />
  <button onclick="addTask()">Add</button>
  <ul id="task-list"></ul>
</div>
<script>
  function loadTasks() {
    const tasks = JSON.parse(localStorage.getItem('tasks') || '[]');
    const taskList = document.getElementById('task-list');
    taskList.innerHTML = '';
    tasks.forEach((task, index) => {
      taskList.innerHTML += `<li>${task} <button onclick="removeTask(${index})">x</button></li>`;
    });
  }
  function addTask() {
    const input = document.getElementById('new-task-input');
    const task = input.value.trim();
    if (task) {
      const tasks = JSON.parse(localStorage.getItem('tasks') || '[]');
      tasks.push(task);
      localStorage.setItem('tasks', JSON.stringify(tasks));
      input.value = '';
      loadTasks();
    }
  }
  function removeTask(index) {
    const tasks = JSON.parse(localStorage.getItem('tasks') || '[]');
    tasks.splice(index, 1);
    localStorage.setItem('tasks', JSON.stringify(tasks));
    loadTasks();
  }
  loadTasks();
</script>
```

## Animated Scroll Progress Bar

A fixed progress bar at the top showing scroll progress.

```
xml<!-- Scroll Progress Bar -->
<style>
  #scroll-progress-bar {
    position: fixed;
    top: 0;
    left: 0;
    width: 0;
    height: 5px;
    background: #3498db;
    z-index: 9999;
    transition: width 0.25s ease-out;
  }
</style>
<div id="scroll-progress-bar"></div>
<script>
  window.addEventListener('scroll', () => {
    const scrollTop = window.pageYOffset || document.documentElement.scrollTop;
    const docHeight = document.documentElement.scrollHeight - document.documentElement.clientHeight;
    const scrollPercent = (scrollTop / docHeight) * 100;
    document.getElementById('scroll-progress-bar').style.width = scrollPercent + '%';
  });
</script>
```

## Dark Mode Toggle

Button to toggle light/dark themes with preference saved in localStorage.

```
xml<!-- Dark Mode Toggle -->
<style>
  body.dark-mode { background-color: #121212; color: #eee; }
  #dark-mode-toggle { position: fixed; top: 10px; right: 10px; padding: 10px; background: #444; color: white; border: none; cursor: pointer; z-index: 10000; }
</style>
<button id="dark-mode-toggle">Toggle Dark Mode</button>
<script>
  const toggleButton = document.getElementById('dark-mode-toggle');
  const currentMode = localStorage.getItem('darkMode') || 'light';
  if(currentMode === 'dark') document.body.classList.add('dark-mode');
  toggleButton.addEventListener('click', () => {
    if(document.body.classList.toggle('dark-mode')) {
      localStorage.setItem('darkMode', 'dark');
    } else {
      localStorage.setItem('darkMode', 'light');
    }
  });
</script>
```

## Lazy Loading Image Gallery with Lightbox

Gallery with lazy loading thumbnails and click-to-enlarge lightbox effect.

```
xml<!-- Lazy Loading Image Gallery with Lightbox -->
<style>
  .gallery { display: flex; flex-wrap: wrap; gap: 10px; }
  .gallery img { width: 150px; cursor: pointer; border-radius: 4px; }
  #lightbox { display: none; position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: rgba(0, 0, 0, 0.8); justify-content: center; align-items: center; }
  #lightbox img { max-width: 90%; max-height: 80%; border-radius: 8px; }
</style>
<div class="gallery" id="gallery">
  <img src="image1_thumbnail.jpg" data-full="image1.jpg" loading="lazy" alt="Image 1" />
  <img src="image2_thumbnail.jpg" data-full="image2.jpg" loading="lazy" alt="Image 2" />
  <img src="image3_thumbnail.jpg" data-full="image3.jpg" loading="lazy" alt="Image 3" />
</div>
<div id="lightbox"><img src="" alt="Expanded Image" /></div>
<script>
  const gallery = document.getElementById('gallery');
  const lightbox = document.getElementById('lightbox');
  const lightboxImg = lightbox.querySelector('img');
  gallery.addEventListener('click', e => {
    if(e.target.tagName === 'IMG') {
      lightboxImg.src = e.target.dataset.full;
      lightbox.style.display = 'flex';
    }
  });
  lightbox.addEventListener('click', () => {
    lightbox.style.display = 'none';
  });
</script>
```

## Tag Cloud with Filter

Dynamic tags filter blog posts by tag.

```
xml<!-- Tag Cloud with Filter -->
<style>
  .tag-cloud { margin: 20px 0; }
  .tag { display: inline-block; margin: 0 5px 5px 0; padding: 5px 10px; background: #eee; color: #333; border-radius: 3px; cursor: pointer; }
  .tag.active { background: #3498db; color: white; }
  .posts { margin-top: 20px; }
  .post { display: none; margin-bottom: 10px; border-bottom: 1px solid #ccc; padding-bottom: 10px; }
  .post.visible { display: block; }
</style>
<div class="tag-cloud" id="tag-cloud"></div>
<div class="posts" id="posts"></div>
<script>
  const tags = ['Tech', 'Life', 'Music'];
  const posts = [
    { title: 'Tech Post 1', tags: ['Tech'] },
    { title: 'Life Post 1', tags: ['Life'] },
    { title: 'Music Post 1', tags: ['Music', 'Life'] },
    { title: 'Tech Post 2', tags: ['Tech', 'Music'] }
  ];
  const tagCloud = document.getElementById('tag-cloud');
  const postsContainer = document.getElementById('posts');

  function renderTags() {
    tagCloud.innerHTML = '';
    tags.forEach(tag => {
      const tagElm = document.createElement('span');
      tagElm.className = 'tag';
      tagElm.textContent = tag;
      tagElm.onclick = (event) => filterPosts(event.target, tag);
      tagCloud.appendChild(tagElm);
    });
  }
  function renderPosts(filteredPosts) {
    postsContainer.innerHTML = '';
    filteredPosts.forEach(post => {
      const postElm = document.createElement('div');
      postElm.className = 'post visible';
      postElm.textContent = post.title + ' - Tags: ' + post.tags.join(', ');
      postsContainer.appendChild(postElm);
    });
  }
  function filterPosts(tagElement, tag) {
    const activeTag = document.querySelector('.tag.active');
    if(activeTag) activeTag.classList.remove('active');
    tagElement.classList.add('active');
    const filtered = posts.filter(post => post.tags.includes(tag));
    renderPosts(filtered);
  }
  renderTags();
  renderPosts(posts);
</script>
```

## Random Quote Generator

Rotate quotes on load or button click.

```
xml<!-- Random Quote Generator -->
<div id="quote-box"></div>
<button onclick="newQuote()">New Quote</button>
<script>
  const quotes = [
    'The best way to get started is to quit talking and begin doing.',
    'Don’t let yesterday take up too much of today.',
    'It’s not whether you get knocked down, it’s whether you get up.',
    'If you are working on something exciting, it will keep you motivated.',
  ];
  function newQuote() {
    const randIndex = Math.floor(Math.random() * quotes.length);
    document.getElementById('quote-box').innerText = quotes[randIndex];
  }
  newQuote();
</script>
```

## Custom Search Widget

Client-side search on JSON site content index.

```
xml<!-- Custom Search Widget -->
<input type="text" id="search-input" placeholder="Search..." />
<ul id="search-results"></ul>
<script>
  const contentIndex = [
    { title: 'First Post', content: 'This is the first post content' },
    { title: 'Second Post', content: 'Second post content here' }
  ];
  const input = document.getElementById('search-input');
  const results = document.getElementById('search-results');
  input.addEventListener('input', () => {
    const query = input.value.toLowerCase();
    results.innerHTML = '';
    if(query.length > 0) {
      const filtered = contentIndex.filter(item =>
        item.title.toLowerCase().includes(query) || item.content.toLowerCase().includes(query)
      );
      filtered.forEach(item => {
        const li = document.createElement('li');
        li.textContent = item.title + ': ' + item.content;
        results.appendChild(li);
      });
    }
  });
</script>
```

## Weather Widget

Shows weather by user location using OpenWeatherMap API.

```
xml<!-- Weather Widget -->
<div id="weather-widget">Loading weather...</div>
<script>
  async function fetchWeather() {
    try {
      const locRes = await fetch('https://ipapi.co/json/');
      const locData = await locRes.json();
      const lat = locData.latitude;
      const lon = locData.longitude;
      const apiKey = 'your_openweather_api_key';
      const weatherRes = await fetch(
        `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=metric&appid=${apiKey}`
      );
      const weatherData = await weatherRes.json();
      document.getElementById('weather-widget').innerText =
        `Weather in ${weatherData.name}: ${weatherData.weather[0].description}, ${weatherData.main.temp}°C`;
    } catch (e) {
      document.getElementById('weather-widget').innerText = 'Unable to fetch weather data.';
    }
  }
  fetchWeather();
</script>
```

## Countdown Timer

Countdown to a specified date/time.

```
xml<!-- Countdown Timer -->
<div id="countdown"></div>
<script>
  const targetDate = new Date('2025-12-31T23:59:59');
  const countdown = document.getElementById('countdown');
  function updateCountdown() {
    const now = new Date();
    const diff = targetDate - now;
    if(diff <= 0) {
      countdown.innerText = 'Event started!';
      clearInterval(intervalId);
      return;
    }
    const days = Math.floor(diff / (1000 * 60 * 60 * 24));
    const hours = Math.floor((diff / (1000 * 60 * 60)) % 24);
    const minutes = Math.floor((diff / (1000 * 60)) % 60);
    const seconds = Math.floor((diff / 1000) % 60);
    countdown.innerText = `${days}d ${hours}h ${minutes}m ${seconds}s remaining`;
  }
  updateCountdown();
  const intervalId = setInterval(updateCountdown, 1000);
</script>
```

## Animated Background Effects

Subtle background animation with CSS gradient animation.

```
xml<!-- Animated Background Effects -->
<style>
  body {
    margin: 0;
    overflow: hidden;
    background: linear-gradient(270deg, #ff6ec4, #7873f5, #4ade80);
    background-size: 600% 600%;
    animation: BackgroundGradient 15s ease infinite;
  }
  @keyframes BackgroundGradient {
    0% {background-position:0% 50%;}
    50% {background-position:100% 50%;}
    100% {background-position:0% 50%;}
  }
</style>
```

These can be converted into Hugo partial files as you requested or used standalone. For API integrations (Last.fm, weather), replace placeholders like API keys and usernames with your own values. Let me know if you want me to prepare a specific partial file format or help with integration!

1. [http://last.fm](<http://last.fm/>)



