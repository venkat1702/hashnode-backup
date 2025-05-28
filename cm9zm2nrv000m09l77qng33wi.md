---
title: "Why Some Pages Load Instantly While Others Lag"
seoTitle: "Critical Rendering Path Explained: Why Pages Load Fast or Lag"
seoDescription: "Understand the critical rendering path and why it affects page load times. Learn practical tips to optimize it for faster, smoother websites."
datePublished: Sun Apr 27 2025 12:14:02 GMT+0000 (Coordinated Universal Time)
cuid: cm9zm2nrv000m09l77qng33wi
slug: why-some-pages-load-instantly-while-others-lag
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1746122967868/a9bf9a0c-7855-47f6-85a5-d42cdca752ee.png
tags: css, javascript, performance, web-development, seo, webdev, html5, frontend-development, performance-optimization

---

Google ranks faster pages higher. Users abandon slow sites in seconds.  
The first step toward building fast experiences is mastering the **Critical Rendering Path (CRP)** — how browsers convert **code to pixels**.

In this blog, we’ll dive deep into:

* How browsers handle HTML, CSS, and JavaScript.
    
* Step-by-step explanation of rendering.
    
* What causes slow rendering (render blockers).
    
* Practical ways to optimize and measure it.
    

No frameworks. Pure **HTML, CSS, and JavaScript** — so you understand the raw fundamentals.

## How Browser Converts Code into Pixels

### Step 1: Parsing HTML → Building DOM

* The browser reads raw **HTML** text.
    
* Converts it into **tokens**: start tags, attributes, text nodes, end tags.
    
* Tokens are assembled into **nodes**.
    
* Nodes are linked to form the **DOM Tree**.
    

```xml
<html>
  <body>
    <h1>Hello World</h1>
    <div style="display:none;">Hidden</div>
  </body>
</html>
```

```plaintext
html
 └── body
      └── h1 (Hello World)
      └── div (Hidden)
```

### Step 2: Parsing CSS → Building CSSOM

* **CSS** is also parsed into tokens.
    
* Tokens → Objects → CSSOM Tree.
    
* CSSOM represents styles, rules, selectors, inheritance, and cascading.
    

```css
h1 {
  color: blue;
  font-size: 32px;
}
```

```plaintext
Rule: h1 { color: blue; font-size: 32px; }
```

> ❗ Until CSSOM is built, the browser can't render anything, because styles affect visibility, size, and positioning.

### Step 3: Constructing the Render Tree

* **DOM + CSSOM → Render Tree**.
    
* Only **visible elements** are included.
    
* Hidden elements (`display: none`) are excluded.
    

### Step 4: Layout (Reflow)

* The browser computes the **geometry**: positions, dimensions.
    
* Calculates things like:
    
    * Where does the div start?
        
    * How wide is the button?
        
    * How much height does the paragraph need?
        

🎯 **Note**: Layout can be **complex** — especially for responsive sites. Media queries, Flexbox, Grids all affect layout computation.

### Step 5: Painting

* The browser paints **pixels** to the screen based on layout and style info.
    
* Each layer (backgrounds, borders, shadows, text) is drawn.
    
* GPU acceleration may be used for smoother animations and scrolling.
    

🎯 **Note**: **Heavy paint operations** (like box-shadows, complex gradients) can hurt performance!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745747516315/db76a3c3-a4cb-4e58-8f7c-9f9b827651a9.png align="center")

## Real-World Demo (HTML + CSS + JS)

```xml
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>CRP Demo</title>
  <style>
    .visible {
      color: green;
      font-size: 24px;
    }
    .hidden {
      display: none;
    }
  </style>
</head>
<body>
  <div id="text" class="visible">Hello!</div>
  <div class="hidden">You can't see me</div>

  <script>
    document.getElementById('text').innerText = "Hello, CRP World!";
  </script>
</body>
</html>
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745748506893/26ffd37d-ec20-4994-81d9-ab87d52adabc.png align="center")

## What Are Render Blockers?

Render-blockers **delay page load** by preventing the browser from building DOM, CSSOM, or Render Tree.

**Main Render Blockers**:

| **Resource Type** | **Why It Blocks** |
| --- | --- |
| CSS Files | Must be fully downloaded and parsed before rendering |
| Synchronous JS (`<script>`) | JS can modify DOM/CSSOM, so browser must pause parsing |
| Fonts | Can block text painting (FOIT: Flash of Invisible Text) |

## How to Optimize the Critical Rendering Path

**Optimization Goals**:

* Minimize **Render Blocking** resources.
    
* Prioritize **above-the-fold content**.
    
* Defer non-critical resources.
    
* Reduce size and complexity.
    

### 1\. Minimize Critical Resources

* Reduce size of CSS and JavaScript.
    
* Keep critical CSS small — only for first-screen view. or go with inline css
    

**Example**:

```xml
<style>
  /* Critical styles */
  header { background: blue; color: white; }
</style>
```

---

### 2\. Defer or Async JavaScript

* Prevent JS from blocking DOM parsing.
    

**Example**:

```xml
<script src="app.js" {async/defer}></script>
```

* `async` loads and executes as soon as possible.
    
* `defer` ensures JS runs **after** HTML parsing.
    

---

### 3\. Split CSS (Critical and Non-Critical)

* Inline Critical CSS.
    
* Load the rest with `media="print"` trick: This trick prompts the browser to deprioritize the stylesheet because it's meant for printing
    

**Example**:

```xml
<link rel="stylesheet" href="non-critical.css" media="print" onload="this.media='all'">
```

---

### 4\. Prioritize Above-the-Fold Content

* Focus on what users see first.
    
* Lazy-load everything else.
    

**Example**:  
Use `loading="lazy"` for images:

```xml
<img src="hero.jpg" alt="Hero" loading="lazy">
```

---

### 5\. Font Optimization

* Fonts can block painting.
    
* Preload important fonts:
    
* Use font-display: swap to Avoid **FOIT(Flash of invisible text)**
    
    The `font-display` property controls how the browser behaves during font loading.
    

**Example**:

```xml
<link rel="preload" href="/fonts/font.woff2" as="font" type="font/woff2" crossorigin="anonymous">
```

**Example**:

```css
@font-face {
  font-family: 'CustomFont';
  src: url('/fonts/font.woff2') format('woff2');
  font-display: swap;
}
```

---

## Part 5: How to Analyze Critical Rendering Path in Chrome DevTools

**Steps**:

1. Open DevTools → **Performance** tab.
    
2. Start recording, reload the page.
    
3. Analyze:
    
    * **Screenshots timeline** (slow to paint?)
        
    * **Long tasks** (big scripting or layout blocking?)
        
    * **Waterfall** (which resource blocks rendering?)
        

**Use Lighthouse**:

* Find render-blocking resources.
    
* See opportunities for deferring.
    

**Use Coverage Tab**:

* Find unused CSS and JS.
    
* Remove dead code.
    

---

## Deep Dive on CSS Precedence

CSS is **Cascading**. Rules:

* Inline styles &gt; IDs &gt; Classes &gt; Elements.
    
* If two rules have same specificity, the **last loaded rule wins**.
    

Always load critical styles **before** non-critical styles to avoid FOUC (Flash of Unstyled Content).

---

## Final Checklist

✅ Inline only above-the-fold CSS.  
✅ Defer all non-critical JS.  
✅ Load non-essential CSS asynchronously.  
✅ Minimize CSS and JS file sizes.  
✅ Preload fonts, images, and key resources.  
✅ Use lazy loading for images/videos.  
✅ Audit with DevTools and Lighthouse regularly.

---

## **So glad you made it here! 🙌**

Thanks for checking this out. If you’d like to know more about me, here’s where to go: 👉 [**\[View**](https://www.myvcodes.com/)