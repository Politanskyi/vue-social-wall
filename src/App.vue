<template>
	<div class="container">
		<div class="row">
			<div class="col-sm-6 my-3 mx-auto">
				<form @submit.prevent="addWallPost">
					<div>
						<label for="wall">Wall</label>
						<textarea @click="resizeWall"
								  :rows="wallRows"
								  v-model="wallPost.text"
								  id="wall"
								  class="form-control"
								  placeholder="Whats new?"></textarea>
					</div>
					<div class="mt-2">
						<button type="submit"
								class="btn btn-primary"
								:disabled="checkMessage">
							Submit
						</button>
					</div>
				</form>

				<div v-for="(wallPost) in wallPosts"
					 class="card mt-3"
					 :key="wallPost.id">
					<div class="card-body">
						<div class="wall">
							<div class="wall__post">
								<div class="wall__text">{{ wallPost.text }}</div>

								<div class="wall__actions">
									<i @click="editWallPost(wallPost)" class="bi bi-pencil"></i>
									<i @click="deleteWallPost(wallPost.id)" class="bi bi-x-circle"></i>
								</div>
							</div>

							<div v-if="editPost === wallPost"
								 class="wall__edit">
								<textarea v-model="editText"
										  class="form-control"></textarea>

								<button type="submit"
										class="btn btn-primary mt-2"
										@click="editWallPostSubmit">
									Save changed Post
								</button>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	data() {
		return {
			wallRows: 1,
			wallCount: 0,
			wallPosts: [],
			wallPost: {
				text: ''
			},
			editPost: {},
			editText: ''
		}
	},
	created() {
		const savedPosts = localStorage.getItem('posts-items');

		if (savedPosts) {
			this.wallPosts = JSON.parse(savedPosts);
			this.wallCount = this.wallPosts[0].id;
		}
	},
	methods: {
		resizeWall() {
			this.wallRows = 3
		},
		addWallPost() {
			this.wallCount++;

			this.wallPosts.unshift({
				id: this.wallCount,
				text: this.wallPost.text
			});

			this.wallPost.text = '';
		},
		deleteWallPost(id) {
			const postId = this.wallPosts.findIndex(post => post.id === id);

			if (postId !== -1) {
				this.wallPosts.splice(postId, 1);
			}
		},
		editWallPost(post) {
			this.editPost = post;
			this.editText = post.text;
		},
		editWallPostSubmit() {
			this.editPost.text = this.editText;
			this.editPost = null;
		}

	},
	computed: {
		checkMessage() {
			if (!this.wallPost.text) {
				return true;
			}
		}
	},
	watch: {
		wallPosts: {
			handler() {
				localStorage.setItem('posts-items', JSON.stringify(this.wallPosts));
			},
			deep: true
		}
	}
}
</script>

<style lang="scss">
.bi {
	padding: 0 5px;
	color: #b4b4b4;
	cursor: pointer;
	transition: color .2s;

	&:hover {
		color: #5f9fe9;

		&:last-child {
			color: #e95f5f;
		}
	}
}

.wall {
	display: block;

	&__post {
		display: flex;
		justify-content: space-between;
	}

	&__actions {
		display: flex;
		margin-left: 15px;
		flex-shrink: 0;

		&::before {
			content: '';
			display: block;
			width: 1px;
			height: 100%;
			margin-right: 5px;
			background-color: #b4b4b4;
		}
	}

	&__edit {
		margin-top: 20px;
	}
}
</style>
