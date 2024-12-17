<template>
  <div class="api-demo">
    <h1>API Methods Demo</h1>
    
    <!-- Buttons for Different API Actions -->
    <div>
      <button @click="fetchAllPosts">Fetch All Posts</button>
      <button @click="fetchPostById(1)">Fetch Post By ID</button>
      <button @click="fetchCommentsByPostId(1)">Fetch Comments</button>
      <button @click="createPost">Create Post</button>
      <button @click="updatePost">Update Post (PUT)</button>
      <button @click="patchPost">Update Post (PATCH)</button>
      <button @click="deletePost">Delete Post</button>
    </div>

    <!-- Display Posts -->
    <h2>Posts</h2>
    <ul>
      <li v-for="post in posts" :key="post.id">
        <strong>{{ post.id }}.</strong> {{ post.title }}
      </li>
    </ul>

    <!-- Display Comments -->
    <h2>Comments</h2>
    <ul>
      <li v-for="comment in comments" :key="comment.id">
        <strong>{{ comment.name }}:</strong> {{ comment.body }}
      </li>
    </ul>

    <!-- Display Error Messages -->
    <p v-if="errorMessage" style="color: red;">{{ errorMessage }}</p>
  </div>
</template>

<script>
export default {
  name: 'ApiMethodsDemo',
  data() {
    return {
      posts: [],        // Holds fetched posts
      comments: [],     // Holds fetched comments
      errorMessage: '', // Holds any error messages
    };
  },
  methods: {
    // GET: Fetch All Posts
    async fetchAllPosts() {
      this.errorMessage = '';
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts');
        if (!response.ok) throw new Error('Failed to fetch posts');
        this.posts = await response.json();
        console.log('All posts fetched:', this.posts);
      } catch (error) {
        this.handleError(error);
      }
    },

    // GET: Fetch Single Post by ID
    async fetchPostById(id) {
      this.errorMessage = '';
      try {
        const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
        if (!response.ok) throw new Error(`Failed to fetch post with ID: ${id}`);
        const post = await response.json();
        this.posts = [post];
        console.log('Post fetched:', post);
      } catch (error) {
        this.handleError(error);
      }
    },

    // GET: Fetch Comments for a Specific Post
    async fetchCommentsByPostId(postId) {
      this.errorMessage = '';
      try {
        const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${postId}/comments`);
        if (!response.ok) throw new Error('Failed to fetch comments');
        this.comments = await response.json();
        console.log('Comments fetched:', this.comments);
      } catch (error) {
        this.handleError(error);
      }
    },

    // POST: Create a New Post
    async createPost() {
      this.errorMessage = '';
      try {
        const newPost = {
          title: 'New Post',
          body: 'This is a newly created post.',
          userId: 1,
        };

        const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(newPost),
        });

        if (!response.ok) throw new Error('Failed to create post');
        const data = await response.json();
        this.posts.push(data);
        console.log('Post created:', data);
      } catch (error) {
        this.handleError(error);
      }
    },

    // PUT: Update an Entire Post
    async updatePost() {
      this.errorMessage = '';
      try {
        const updatedPost = {
          id: 1,
          title: 'Updated Post Title',
          body: 'This is an updated post using PUT.',
          userId: 1,
        };

        const response = await fetch('https://jsonplaceholder.typicode.com/posts/1', {
          method: 'PUT',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(updatedPost),
        });

        if (!response.ok) throw new Error('Failed to update post');
        const data = await response.json();
        console.log('Post updated with PUT:', data);
      } catch (error) {
        this.handleError(error);
      }
    },

    // PATCH: Partially Update a Post
    async patchPost() {
      this.errorMessage = '';
      try {
        const partialUpdate = { title: 'Patched Post Title' };

        const response = await fetch('https://jsonplaceholder.typicode.com/posts/1', {
          method: 'PATCH',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(partialUpdate),
        });

        if (!response.ok) throw new Error('Failed to patch post');
        const data = await response.json();
        console.log('Post updated with PATCH:', data);
      } catch (error) {
        this.handleError(error);
      }
    },

    // DELETE: Delete a Post
    async deletePost() {
      this.errorMessage = '';
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts/1', {
          method: 'DELETE',
        });

        if (!response.ok) throw new Error('Failed to delete post');
        console.log('Post deleted successfully');
        this.posts = this.posts.filter((post) => post.id !== 1);
      } catch (error) {
        this.handleError(error);
      }
    },

    // Error Handling Function
    handleError(error) {
      this.errorMessage = error.message;
      console.error(error.message);
    },
  },
};
</script>

<style>
.api-demo {
  font-family: Arial, sans-serif;
  margin: 20px;
}

button {
  margin: 5px;
  padding: 10px;
  background-color: #4CAF50;
  color: white;
  border: none;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}
</style>
