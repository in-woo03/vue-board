<template>
    <div>
        <div class="comment-list-item">
            <div class="comment-list-item-name">
                <div>{{name}}</div>
                <div>{{commentObj.created_at}}</div>
            </div>
            <div class="comment-list-item-context">{{commentObj.context}}</div>
            <div class="comment-list-item-button">
                <b-button variant="info" @click="updateComment">수정</b-button>
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
        const comment_index = data.Comment.findIndex(item => item.comment_id === this.commentId);
        data.Comment.splice(comment_index, 1) //삭제 구현
        this.$router.push({
            path: `/board/free/detail/${this.contentId}` // contentId를 사용하여 경로 설정
        })
      },
      updateComment() {
        this.$router.push({
          path: `/board/free/detail/${this.contentId}`
        })
      }
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