<template>
    <div class="container">
        <div class="row">
            <div class="col-md-12">
                <div class="card">
                    <div class="card-header">
                        Comments:
                    </div>
                    <div class="card-block">
                        <div class="p-2 level1 card" v-for="(item, level1_index) in comment">
                            <div class="name font-weight-bold card-header">{{ item.name }}</div>
                            <div class="message card-block pl-3">{{ item.message }}</div>
                            <div class="level2 p-2 ml-3 card" v-if="item.level2 != null" v-for="(item_level2, level2_index) in item.level2">
                                <div class="name font-weight-bold card-header">{{ item_level2.name }}</div>
                                <div class="message card-block pl-3">{{ item_level2.message }}</div>
                                <div class="level3 p-2 ml-3 card" v-if="item_level2.level3 != null" v-for="item_level3 in item_level2.level3">
                                    <div class="name font-weight-bold card-header">{{ item_level3.name }}</div>
                                    <div class="message card-block pl-3">{{ item_level3.message }}</div>
                                </div>
                                <span><a :href="'#collapseCommentBox' + level1_index + level2_index" data-toggle="collapse" aria-expanded="false">Comment</a></span>
                                <div class="collapse border rounded p-2 col-md-12" :id="'collapseCommentBox' + level1_index + level2_index">
                                    <div class="comment-box col-md-5">
                                        <div class="col-md-12 form-group">
                                            <label for="name" class="col-md-4 control-label">Name: </label>
                                            <input type="text" v-model="data['level2'][level2_index]['name']" value="">
                                            <div class="alert alert-danger" v-if="errors.length > 0 && 'name' in errors[0] && errors[0].name.length > 0">
                                                <ul class="list-unstyled">
                                                    <li>{{ errors[0].name[0] }}</li>
                                                </ul>
                                            </div>
                                        </div>
                                        <div class="col-md-12 form-group">
                                            <label for="message" class="col-md-4 control-label">Message: </label>
                                            <textarea v-model="data['level2'][level2_index]['message']" text=""></textarea>
                                            <div class="alert alert-danger" v-if="errors.length > 0 && 'message' in errors[0] && errors[0].message.length > 0">
                                                <ul class="list-unstyled">
                                                    <li>{{ errors[0].message[0] }}</li>
                                                </ul>
                                            </div>
                                        </div>
                                        <div class="col-md-12 form-group text-right">
                                            <button type="button" @click="createComment(item_level2.id,'level2',level2_index)" class="btn btn-primary">Submit</button>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <span><a :href="'#collapseCommentBox' + level1_index" data-toggle="collapse" aria-expanded="false">Comment</a></span>
                            <div class="collapse border rounded p-2 col-md-12" :id="'collapseCommentBox' + level1_index">
                                <div class="comment-box col-md-4">
                                    <div class="col-md-12 form-group">
                                        <label for="name" class="col-md-4 control-label">Name: </label>
                                        <input type="text" v-model="data['level1'][level1_index]['name']">
                                        <div class="alert alert-danger" v-if="errors.length > 0 && 'name' in errors[0] && errors[0].name.length > 0">
                                            <ul class="list-unstyled">
                                                <li>{{ errors[0].name[0] }}</li>
                                            </ul>
                                        </div>
                                    </div>
                                    <div class="col-md-12 form-group">
                                        <label for="message" class="col-md-4 control-label">Message: </label>
                                        <textarea v-model="data['level1'][level1_index]['message']"></textarea>
                                        <div class="alert alert-danger" v-if="errors.length > 0 && 'message' in errors[0] && errors[0].message.length > 0">
                                            <ul class="list-unstyled">
                                                <li>{{ errors[0].message[0] }}</li>
                                            </ul>
                                        </div>
                                    </div>
                                    <div class="col-md-12 form-group text-right">
                                        <button type="button" @click="createComment(item.id,'level1',level1_index)" class="btn btn-primary">Submit</button>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="border rounded p-2 col-md-12">
                            <div class="comment-box col-md-4">
                                <div class="col-md-12 form-group">
                                    <label for="name" class="col-md-4 control-label">Name: </label>
                                    <input type="text" v-model="comment_box.name">
                                    <div class="alert alert-danger" v-if="errors.length > 0 && 'name' in errors[0] && errors[0].name.length > 0">
                                        <ul class="list-unstyled">
                                            <li>{{ errors[0].name[0] }}</li>
                                        </ul>
                                    </div>
                                </div>
                                <div class="col-md-12 form-group">
                                    <label for="message" class="col-md-4 control-label">Message: </label>
                                    <textarea v-model="comment_box.message"></textarea>
                                    <div class="alert alert-danger" v-if="errors.length > 0 && 'message' in errors[0] && errors[0].message.length > 0">
                                        <ul class="list-unstyled">
                                            <li>{{ errors[0].message[0] }}</li>
                                        </ul>
                                    </div>
                                </div>
                                <div class="col-md-12 form-group text-right">
                                    <button type="button" @click="createComment(null, null, null)" class="btn btn-primary">Submit</button>
                                </div>
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
        data(){
            return {
                comment: [],
                comment_box: {
                    name: '',
                    message: '',
                    parent_id: ''
                },
                data: {
                    'level1': [],
                    'level2': []
                },
                errors: []
            }
        },
        mounted() {
            this.initComments();
        },
        methods: {
            initComments(){
                axios.get('/comment').then(response => {
                    var mythis = this;
                    for(var x in response.data.comment){
                        mythis.data['level1'][x] = {'name': '', 'message': ''};
                        for(var y in response.data.comment[x].level2){
                            mythis.data['level2'][y] = {'name': '', 'message': ''};
                        }
                    }
                    this.comment = response.data.comment;
                });
            },
            createComment(parent_id, level, index){
                var mythis = this;
                axios.post('/comment', {
                    name: level == null? mythis.comment_box.name : mythis.data[level][index]['name'],
                    message: level == null? mythis.comment_box.message : mythis.data[level][index]['message'],
                    parent_id: parent_id
                })
                .then(response => {
                    mythis.initComments();
                })
                .catch(error => {
                    mythis.errors = [];
                    if (error.response && error.response.status === 400) {
                        mythis.errors.push(error.response.data.messages);
                    }
                });
            }
        }
    }
</script>