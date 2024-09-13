<template>
    <div>
        <div class="comment-list-item">
            <div class="comment-list-item-name">
                <div>{{name}}</div>
                <div>{{commentObj.created_at}}</div>
            </div>
<!--             <div class="comment-list-item-button">
                <b-button variant="info" @click="startEdit">수정</b-button>
                <b-button variant="info" @click="deleteComment">삭제</b-button>
                <b-button variant="info" @click="subCommentToggle">덧글 달기</b-button>
            </div> -->

            <div v-if="isEditing">
                <b-form-textarea v-model="editContext" rows="4" style="width: 600px;"></b-form-textarea>
                <br>
                <b-button variant="success" @click="saveComment">저장</b-button>
                <b-button variant="danger" @click="cancelEdit">취소</b-button>
            </div>
            <div v-else class="comment-list-item-context">{{commentObj.context}}</div>

            <div class="comment-list-item-button">
                <b-button variant="info" @click="startEdit">수정</b-button>
                <b-button variant="info" @click="deleteComment">삭제</b-button>
                <b-button variant="info" @click="subCommentToggle">덧글 달기</b-button>
            </div>
        </div>
        <!-- subCommentCreateToggle가 true일 때만 CommentCreate 보여줌 -->
        <template v-if="subCommentCreateToggle">
          <CommentCreate 
            :isSubComment="true"
            :commentId="commentObj.comment_id"
            :reloadSubComments="reloadSubComments"
            :subCommentToggle="subCommentToggle"
          />
        </template>
        <!-- 서브 댓글 -->
        <template v-if="subCommentList.length > 0">
          <div
            class="comment-list-item-subcomment-list"
            :key="item.subcomment_id"
            v-for="item in subCommentList"
          >
            <div class="comment-list-item-name">
              <div>{{item.user_name}}</div>
              <div>{{item.created_at}}</div>
            </div>
            <div class="comment-list-item-context">{{item.context}}</div>
            <div class="comment-list-item-button">
              <b-button variant="info">수정</b-button>
              <b-button variant="info">삭제</b-button>
            </div>
          </div>
        </template>
        <!-- 서브 댓글 -->
    </div>
</template>

<script>
import data from '@/data';
import CommentCreate from "./CommentCreate";

export default {
    name: 'CommentListItem', 
    props: {
        commentObj: Object,
        contentId: Number,
    },
    components: {
      CommentCreate
    },
    data() {
        return {
            name: data.User.filter(
              item => item.user_id === this.commentObj.user_id
            )[0].name,
            //한 코멘트에 달린 서브 댓글 가져오기
            subCommentList: data.SubComment.filter(
              item => item.comment_id === this.commentObj.comment_id
            ).map(subCommentItem => ({
              ...subCommentItem,
              user_name: data.User.filter(
                item => item.user_id === subCommentItem.user_id
              )[0].name
            })),
            subCommentCreateToggle: false,
            editContext: this.commentObj.context, // 수정할 내용을 위한 변수
            isEditing: false, // 수정 모드 여부
        };
    },
    methods: {
      //subCommentCreateToggle을 스위칭하는 역할
      subCommentToggle() {
        this.subCommentCreateToggle = !this.subCommentCreateToggle;
      },
      reloadSubComments() {
        this.subCommentList = data.SubComment.filter(
          item => item.comment_id === this.commentObj.comment_id
        ).map(subCommentItem => ({
          ...subCommentItem,
          user_name: data.User.filter(
            item => item.user_id === subCommentItem.user_id
          )[0].name
        }));
      },
      deleteComment() {
        const commentIndex = data.Comment.findIndex(item => item.comment_id === this.commentObj.comment_id);
        if (commentIndex !== -1) {
            data.Comment.splice(commentIndex, 1); // 댓글 삭제
            this.$emit('reloadComment'); // 부모 컴포넌트에 댓글 목록 리로드 요청
        }
      },
      startEdit() {
            this.editContext = this.commentObj.context; // 현재 댓글 내용을 넣기
            this.isEditing = true; // 수정 모드 활성화
      },
      saveComment() {
          const commentIndex = data.Comment.findIndex(item => item.comment_id === this.commentObj.comment_id);
          if (commentIndex !== -1) {
              data.Comment[commentIndex].context = this.editContext; // 수정된 내용 저장
          }
          this.isEditing = false; // 수정 모드 비활성화
          this.reloadSubComments(); // 댓글 목록 갱신
      },
      cancelEdit() {
          this.isEditing = false; // 수정 모드 비활성화
      },
    }
}
</script>

<style scoped>
.comment-list-item {
  display: flex;
  justify-content: space-between;
  padding-bottom: 1em;
}

.comment-list-item-name {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  border: 0.5px solid black;
  padding: 1em;
}

.comment-list-item-context {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 50em;
  border: 0.5px solid black;
}

.comment-list-item-button {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  border: 0.5px solid black;
  padding: 1em;
}

.btn {
  margin-bottom: 1em;
}

.comment-list-item-subcomment-list {
  display: flex;
  justify-content: space-between;
  padding-bottom: 1em;
  margin-left: 10em;
}
</style>