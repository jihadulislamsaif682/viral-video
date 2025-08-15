<!DOCTYPE html>
<html lang="bn">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Meme Hub</title>
<style>
    body {
        font-family: Arial, sans-serif;
        background: #f4f4f4;
        margin: 0;
        padding: 0;
    }
    header {
        background: #ff4081;
        color: white;
        padding: 15px;
        text-align: center;
        font-size: 24px;
    }
    .container {
        max-width: 800px;
        margin: auto;
        padding: 10px;
        background: white;
        border-radius: 10px;
    }
    .new-post {
        margin-bottom: 20px;
    }
    input, textarea {
        width: 100%;
        padding: 8px;
        margin: 5px 0;
        border: 1px solid #ccc;
        border-radius: 5px;
    }
    button {
        padding: 8px 15px;
        background: #ff4081;
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
    }
    .video-post {
        border: 1px solid #ddd;
        padding: 10px;
        margin-top: 10px;
        border-radius: 8px;
        background: #fafafa;
    }
    video {
        width: 100%;
        border-radius: 8px;
    }
    .likes {
        color: #ff4081;
        cursor: pointer;
        margin-top: 5px;
        display: inline-block;
    }
    .comment-section {
        margin-top: 10px;
    }
    .comment {
        background: #eee;
        padding: 5px;
        border-radius: 5px;
        margin-top: 3px;
    }
</style>
</head>
<body>

<header>Meme Hub 🎭</header>

<div class="container">
    <div id="loginDiv" class="new-post">
        <h3>Admin Login</h3>
        <input type="password" id="adminPin" placeholder="Enter Admin PIN">
        <button onclick="login()">Login</button>
    </div>

    <div id="postDiv" class="new-post" style="display:none;">
        <h3>Add New Meme Video</h3>
        <input type="text" id="videoTitle" placeholder="Video Title">
        <input type="text" id="videoUrl" placeholder="Video URL (YouTube, TikTok, FB, Instagram, or MP4)">
        <button onclick="addPost()">Add Meme</button>
    </div>

    <h3>All Memes</h3>
    <div id="posts"></div>
</div>

<script>
let adminLogged = false;
let posts = JSON.parse(localStorage.getItem('memePosts') || '[]');

function login() {
    const pin = document.getElementById('adminPin').value;
    if (pin === "1234") {
        adminLogged = true;
        document.getElementById('loginDiv').style.display = 'none';
        document.getElementById('postDiv').style.display = 'block';
        alert("Admin Logged In!");
    } else {
        alert("Wrong PIN!");
    }
}

function renderPosts() {
    const container = document.getElementById('posts');
    container.innerHTML = '';
    posts.slice().reverse().forEach((p, index) => {
        let div = document.createElement('div');
        div.className = 'video-post';
        let content = '';

        if (p.url.endsWith('.mp4') || p.url.endsWith('.webm')) {
            content = `<video src="${p.url}" controls></video>`;
        } else {
            content = `<a href="${p.url}" target="_blank">Watch Video</a>`;
        }

        div.innerHTML = `<h4>${p.title}</h4>
                         ${content}
                         <div class="likes" onclick="likePost(${index})">❤️ ${p.likes || 0} Likes</div>
                         <div class="comment-section">
                             <input type="text" placeholder="Write a comment" onkeypress="addComment(event, ${index})">
                             ${(p.comments || []).map(c => `<div class="comment">${c}</div>`).join('')}
                         </div>`;
        container.appendChild(div);
    });
}

function addPost() {
    if (!adminLogged) return alert("Not authorized");
    const title = document.getElementById('videoTitle').value;
    const url = document.getElementById('videoUrl').value;
    if (!title || !url) return alert("Fill all fields");
    posts.push({ title, url, likes: 0, comments: [] });
    localStorage.setItem('memePosts', JSON.stringify(posts));
    document.getElementById('videoTitle').value = '';
    document.getElementById('videoUrl').value = '';
    renderPosts();
}

function likePost(index) {
    posts[index].likes = (posts[index].likes || 0) + 1;
    localStorage.setItem('memePosts', JSON.stringify(posts));
    renderPosts();
}

function addComment(event, index) {
    if (event.key === "Enter") {
        let comment = event.target.value;
        if (comment.trim() !== '') {
            posts[index].comments = posts[index].comments || [];
            posts[index].comments.push(comment);
            localStorage.setItem('memePosts', JSON.stringify(posts));
            renderPosts();
        }
    }
}

renderPosts();
</script>

</body>
</html>
