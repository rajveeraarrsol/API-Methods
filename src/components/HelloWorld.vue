<template>
  <div class="api-demo">
    <div class="container-fluid">
      <h1>API Methods Demo</h1>

      <div class="d-flex gap-3">
        <button @click="fetchAllPosts">Fetch All Posts</button>
        <button type="button" data-bs-toggle="modal" data-bs-target="#staticBackdrop">Add Post</button>
      </div>

      <ul>
        <li v-for="post, index in posts" :key="post.id">
          <div class="posts-list-list">
            <div class="posts-list-icon">
              <strong>{{ index + 1 }}.</strong> {{ post.title }}
            </div>
            <div class="d-flex gap-3">
              <button v-if="newPostId !== post.id" @click="fetchPostById(post.id)">Details</button>
              <button @click="deletePost(post.id)">Remove</button>
            </div>
          </div>

          <div v-if="expandedPostId === post.id" class="posts-by-id">
            <p>{{ postId.body }}</p>
            <button @click="fetchCommentsByPostId(post.id)">Post Email</button>

            <div class="comments-sec" v-if="comments.length > 0">
              <h2>Post Email</h2>
              <ul>
                <li v-for="comment in comments" :key="comment.id">
                  {{ comment.email }}
                </li>
              </ul>
            </div>
          </div>
        </li>
      </ul>

      <!-- Display Error Messages -->
      <p v-if="errorMessage" style="color: red;">{{ errorMessage }}</p>

      <!-- Modal --> 

      <div class="modal fade" id="staticBackdrop" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="staticBackdropLabel">Create Post</h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
              <form @submit.prevent="createPost">
                <div class="mb-3">
                  <label for="title" class="form-label">Title:</label>
                  <input type="text" class="form-control" id="title" v-model="newPost.title" required
                    placeholder="Enter post title">
                </div>
                <div class="mb-3">
                  <label for="body" class="form-label">Body:</label>
                  <textarea class="form-control" id="body" v-model="newPost.body" required
                    placeholder="Enter post content" rows="3"></textarea>
                </div>
                <button type="submit" class="btn btn-primary" data-bs-dismiss="modal">Save</button>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ApiMethodsDemo',
  data() {
    return {
      posts: [],
      postId: {},
      comments: [],
      errorMessage: '',
      expandedPostId: null,
      newPostId: null,
      newPost: {
        title: '',
        body: '',
      },
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
      } catch (error) {
        this.handleError(error);
      }
    },

    // GET: Fetch Single Post by ID
    async fetchPostById(id) {
      this.errorMessage = '';
      this.expandedPostId = id;
      try {
        const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
        if (!response.ok) throw new Error(`Failed to fetch post with ID: ${id}`);
        this.postId = await response.json();
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
      } catch (error) {
        this.handleError(error);
      }
    },

    // POST: Create a New Post
    async createPost() {
      this.errorMessage = '';
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
          method: 'POST', 
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(this.newPost),
        });
        if (!response.ok) throw new Error('Failed to create post');
        const data = await response.json();
        this.newPostId = data.id;
        this.posts.push(data);
        // Reset the form
        this.newPost.title = '';
        this.newPost.body = '';
      } catch (error) {
        this.handleError(error);
      }
    },

    // DELETE: Delete a Post
    async deletePost(id) {
      this.errorMessage = '';
      try {
        const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`, {
          method: 'DELETE',
        });
        if (!response.ok) throw new Error(`Failed to delete post with ID: ${id}`);
        this.posts = this.posts.filter(post => post.id !== id);
      } catch (error) {
        this.handleError(error);
      }
    },

    // Error Handling Function
    handleError(error) {
      this.errorMessage = error.message;
    },
  },
};
</script>
