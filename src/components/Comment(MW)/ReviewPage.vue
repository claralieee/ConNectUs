<template>
    <div id="view">
        <div class = "comment">
            <reviewBox
            v-bind:style='boxStyle'
            v-bind:message="item.message"
            v-bind:name="item.name"
            v-bind:id="review_id"
            v-bind:user_id="item.user_id"
            v-bind:data="item"
            ></reviewBox>
            <br>
            <br>
            <br>
            <div class="comment-header">Comments:</div>
            <comment-line
            v-bind:data="item"
            v-bind:type=type
            v-bind:reply_type=reply_type
            v-bind:id=review_id
            v-bind:cat=cat
            v-bind:user_id="this.$route.params.user_id">
            </comment-line>
        </div>
        <div class="comment-section">
            <commentBox v-for="reply in replies" v-bind:key="reply.id"
            v-bind:style="boxStyle"
            v-bind:message="reply.message"
            v-bind:name="reply.name"
            v-bind:type="type"
            v-bind:id="reply.id"
            v-bind:data="reply"
            ></commentBox>
        </div>
        <button v-on:click="backButton">Back</button>
    </div>
</template>

<script>
import database from '../../firebase.js'
import reviewBox from './ReviewBox.vue'
import commentLine from './CommentLine.vue'
import commentBox from './CommentBox.vue'
export default {
    data() {
        return {
            review_id: "",
            item: null,
            boxStyle: {
                height: "auto",
                'border-bottom': "hidden"
            },
            dataFetched: false,
            replies: [],
            cat: "Reviews",
            type:"Replies_Reviews",
            reply_type: "review_id"
        }
    },
    methods: {
        fetchReview: async function() {
            database.collection("Reviews")
            .doc(this.review_id)
            .get()
            .then(doc => {
                this.item = doc.data()
            })
        },
        fetchReviewReplies: async function() {
            return database.collection("Replies_Reviews").get().then((querySnapShot)=>{
                    let item = {}
                    querySnapShot.forEach(doc=> { 
                        if (doc.data().review_id == this.review_id){
                            item = doc.data()
                            item["id"] = doc.id
                            this.replies.push(item)
                        }
                    })
                })
        },
        backButton: async function() {
            window.location = "/summary/"+this.item.module+"/"+this.$route.params.user_id
        }
    },
    components: {
        'reviewBox' : reviewBox,
        'commentLine' : commentLine,
        'commentBox' : commentBox,
    },
    async created() {
        this.review_id = this.$route.params.review_id;
        await this.fetchReview()
        await this.fetchReviewReplies()
    }
}
</script>

<style scoped>

#view {
    width: 80%;
    margin: auto;
}

.comment-header {
    width: 188px;
    height: 55.44px;
    color: white;
    font-family: Poppins;
    font-style: normal;
    font-weight: normal;
    background: #BA9977;
    border-radius: 5px;
    font-size: 24px;
    padding-top: 10px;
    text-align: center;
}

button {
    position: absolute;
    right: 0;
    width: 150px;
    margin: 0px 20px 20px 0px;
    height: 56px;
    font-family: Poppins;
    font-style: normal;
    font-weight: normal;
    background: #BA9977;
    border-radius: 5px;
    font-size: 24px;
    color: #FFF;
    border: none;
    cursor: pointer;
    outline:none;
}

button:active {
  background: #e5e5e5;
  -webkit-box-shadow: inset 0px 0px 5px #c1c1c1;
  -moz-box-shadow: inset 0px 0px 5px #c1c1c1;
  box-shadow: inset 0px 0px 5px #c1c1c1;
  outline: none;
}

button:focus {outline:0;}

button:hover {
    transform: scale(1.1);
}




</style>
