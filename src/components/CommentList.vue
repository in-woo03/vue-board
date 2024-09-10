<template>
    <div>
        {{ contentId }}
    </div>
</template>

<script>
import data from '@/data';

export default {
    name: 'CommentList',
    props: {
        contentId: Number,
    },
    data() {
        return {
            comments: data.Comment.filter(item => item.content_id === this.contentId)
        }
    }
}

</script>