<template>
    <div class="comment-create">
        <b-input-group :prepend="name" class="mt-3">
            <b-form-textarea
                id="textarea"
                v-model="context"
                :placeholder="'코멘트를 달아주세요~!'"
                rows="3"
                max-rows="6"
            ></b-form-textarea>
            <b-input-group-append>
                <b-button variant="info" @click="isSubComment ? createSubComment() :createComment()">작성하기</b-button>
            </b-input-group-append>
        </b-input-group>
    </div>
</template>

<script>
import data from "@/data";

export default {
    name: 'CommentCreate',
    props: {    
        contentId: Number,
        commentId: Number,
        isSubComment: Boolean,
        reloadComment: Function,
        subCommentToggle: Function,
        reloadSubComments: Function,
    },
    data() {
        return {
            name: "하양숲",
            context: ""
        };
    },
    methods: {
        createComment() {
            //data의 Comment 배열에 push 해주기
            data.Comment.push({
                comment_id: data.Comment[data.Comment.length - 1].comment_id + 1,
                //데이터의 comment의 가장 마지막에 있는걸 가져오고 새로 푸시할 객체의 comment_id를 정해줌 
                user_id: 1,
                content_id: this.contentId,
                context: this.context,
                created_at: '2019-03-29 14:11:11',
                updated_at: null
            });
            //업로드 후 함수 호출
            this.reloadComment();
            //함수 호출 후 빈칸 유지
            this.comment = ""
        },
        createSubComment() {
            //data의 Comment 배열에 push 해주기
            data.SubComment.push({
                subcomment_id: data.SubComment[data.SubComment.length - 1].subcomment_id + 1,
                //데이터의 comment의 가장 마지막에 있는걸 가져오고 새로 푸시할 객체의 comment_id를 정해줌 
                user_id: 1,
                comment_id: this.commentId,
                context: this.context,
                created_at: '2019-03-29 14:11:11',
                updated_at: null
            });
            //input 닫아주기
            this.subCommentToggle();
            //다시 서브 코멘트 리로드
            this.reloadSubComments();
            //함수 호출 후 빈칸 유지
            this.comment = ""
        }
    }
};
</script>

<style scoped>
.comment-create {
  display: flex;
  margin-bottom: 1em;
}
</style>