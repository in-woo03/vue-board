<template>
    <div>
        <div :key="item.comment_id" v-for="item in comments">
            <!-- v-for를 사용하여 comments의 배열을 하나씩 돌며 보여줌 -->
            <CommentListItem :commentObj="item" @reloadComment="reloadComment"/>
        </div>
        <CommentCreate :contentId="contentId" :reloadComment="reloadComment"/>
        <!-- 아래 props에서 가져와 contentId 넘겨주기 -->
    </div>
</template>

<script>
import data from '@/data';
import CommentListItem from './CommentListItem';
import CommentCreate from './CommentCreate';

export default {
    name: 'CommentList',
    components: {
        CommentListItem,
        CommentCreate,
    },
    props: {
        contentId: Number,
    },
    data() {
        return {
            comments: data.Comment.filter(item => item.content_id === this.contentId)
            // ContentDetail에서 넘겨준 contentId props를 사용
        }
    },
    methods: {
        //댓글 업로드 후 바로 리로드하기
        reloadComment() {
            this.comments = data.Comment.filter(item => item.content_id === this.contentId)
        }
    }
}

</script>

<style scoped>
.comment-create {
  display: flex;
  margin-bottom: 1em;
}
</style>