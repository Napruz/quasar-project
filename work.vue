template>
  <div class="comments-container">
    <!-- Форма добавления нового комментария -->
    <q-form @submit="submitComment" class="comment-form">
      <q-input 
        v-model="newComment.text" 
        label="Добавить комментарий" 
        filled 
        clearable 
        @input="checkLength"
      />
      <div 
        class="char-counter" 
        :class="{ 'char-counter-warning': newComment.text.length >= 950 }"
      >
        {{ newComment.text.length }} / 1000
      </div>
      <div v-if="newComment.text.length > 1000" class="error-message">
        Максимальная длина комментария — 1000 символов
      </div>
      <q-btn label="Отправить" type="submit" color="primary" />
    </q-form>

    <!-- Список комментариев -->
    <ul class="comment-list">
      <li v-for="comment in comments" :key="comment.id" class="comment-item">
        <div class="comment-user-info">
          <q-avatar size="40px" class="comment-avatar">
            <img :src="comment.avatar" alt="User Avatar" />
          </q-avatar>
          <div class="user-meta">
            <div class="user-role">{{ comment.role }}</div>
            <div class="user-department">{{ comment.department }}</div>
          </div>
          <div class="comment-contact">
            <div class="comment-phone">{{ comment.phone }}</div>
            <div class="comment-email">{{ comment.email }}</div>
          </div>
        </div>
        <div class="comment-value">{{ comment.text }}</div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      comments: [
        {
          id: 1,
          avatar: "https://via.placeholder.com/40",
          role: "Менеджер",
          department: "Отдел продаж",
          phone: "+7 (999) 123-45-67",
          email: "user@example.com",
          text: "Отличный курс, всем рекомендую!",
        },
      ],
      newComment: {
        text: "",
      },
    };
  },
  methods: {
    checkLength() {
      if (this.newComment.text.length > 1000) {
        this.newComment.text = this.newComment.text.slice(0, 1000);
      }
    },
    submitComment() {
      if (this.newComment.text.trim() !== "" && this.newComment.text.length <= 1000) {
        this.comments.push({
          id: this.comments.length + 1,
          avatar: "https://via.placeholder.com/40",
          role: "Новый пользователь",
          department: "HR",
          phone: "Не указан",
          email: "Не указан",
          text: this.newComment.text,
        });
        this.newComment.text = "";
      }
    },
  },
};
</script>

<style scoped>
.comments-container {
  max-width: 600px;
  margin: 0 auto;
  padding: 16px;
}

.comment-form {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 20px;
}

.char-counter {
  font-size: 12px;
  color: #555;
  text-align: right;
  margin-bottom: 5px;
}

.char-counter-warning {
  color: red;
}

.error-message {
  color: red;
  font-size: 12px;
  margin-top: -5px;
}

.comment-list {
  list-style: none;
  padding: 0;
}

.comment-item {
  background: #f9f9f9;
  padding: 12px;
  border-radius: 8px;
  margin-bottom: 10px;
}

.comment-user-info {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 8px;
}

.user-meta {
  font-size: 12px;
  color: #555;
}

.comment-contact {
  margin-left: auto;
  text-align: right;
  font-size: 12px;
  color: #555;
}

.comment-value {
  font-size: 14px;
  word-wrap: break-word;
  overflow-wrap: break-word;
  white-space: pre-wrap;
}
</style>
