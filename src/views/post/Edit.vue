<template>
    <div class="container mt-5">
        <div class="row">
            <div class="col-md-12">
                <div class="card border-0 rounded shadow">
                    <div class="card-body">
                        <h4>EDIT POST</h4>
                        <hr>

                        <form @submit.prevent="update">
                            <div class="form-group">
                                <label for="title" class="font-weight-bold">TITLE</label>
                                <input type="text" class="form-control" v-model="post.title" placeholder="Masukkan Judul Post">
                                <!-- validation -->
                                <div v-if="validation.title" class="mt-2 alert alert-danger">
                                    {{ validation.title[0] }}
                                </div>
                            </div>
                            <div class="form-group">
                                <label for="content" class="font-weight-bold">CONTENT</label>
                                <textarea class="form-control" rows="4" v-model="post.content" placeholder="Masukkan Konten Post"></textarea>
                                <!-- validation -->
                                <div v-if="validation.content" class="mt-2 alert alert-danger">
                                    {{ validation.content[0] }}
                                </div>
                            </div>
                            <button type="submit" class="btn btn-primary">SIMPAN</button>
                        </form>                        

                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { reactive, ref, onMounted } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import axios from 'axios'

export default {

    setup() {

        //state posts
        const post = reactive({
            title: '',
            content: ''
        })

        //state validation
        const validation = ref([])

        //vue router
        const router = useRouter()

        //vue route
        const route = useRoute()

        //mounted
        onMounted(() => {

            //get API from Laravel Backend
            axios.get(`http://localhost:8000/api/post/${route.params.id}`)
            .then(response => {
              
              //assign state posts with response data
              post.title    = response.data.data.title  
              post.content  = response.data.data.content  

            }).catch(error => {
                console.log(error.response.data)
            })

        })

        //method update
        function update() {

            let title   = post.title
            let content = post.content

            axios.put(`http://localhost:8000/api/post/${route.params.id}`, {
                title: title,
                content: content
            }).then(() => {

                //redirect ke post index
                router.push({
                    name: 'post.index'
                })

            }).catch(error => {
                //assign state validation with error 
                validation.value = error.response.data
            })

        }

        //return
        return {
            post,
            validation,
            router,
            update
        }

    }

}
</script>

<style>
    body{
        background: lightgray;
    }
</style>