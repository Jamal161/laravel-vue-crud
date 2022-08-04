<template>
    <div class="container">
        <form @submit.prevent="submit">
            <div>
                <label for="title">Title</label>
                <input type="text" v-model="form.title">
            </div>
            <div>
                <label for="body">Body</label>
                <input type="text" v-model="form.body">
            </div>
            <input v-if="idToUpdate === null" type="submit" value="Submit">
            <input v-else type="submit" value="Update">

        </form>
        <div>
            <table v-if="posts">
                <tr>
                    <th>Title</th>
                    <th>Body</th>
                    <th>Actions</th>
                </tr>
                <tr v-for="post in posts" :key="post.id">
                    <td>{{post.title}}</td>
                    <td>{{post.body}}</td>
                     <td>
                        <button @click="edit(post.id)">Edit</button>
                        <button @click="remove(post.id)">Delete</button>
                     </td>
                </tr>
            </table>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
    export default {
        name: 'ExampleComponent',
        data:()=>({
            form:{
                title:null,
                body:null,
            },
            posts:null,
            idToUpdate:null,
        }),
        methods:{
            submit(){
                if(this.idToUpdate === null){
                    axios.post('http://localhost:8000/api/posts', this.form).then(res=>{
                        console.log("res from api", res.data)
                        this.form.title = null,
                        this.form.body = null,
                        this.get()
                    }).catch(err=>{
                        console.log("error ", err.response)
                    })
                }else{
                    axios.put('http://localhost:8000/api/posts/'+this.idToUpdate, this.form).then(res=>{
                        console.log("res ", res.data)
                        this.get();
                        this.idToUpdate = null
                    }).catch(err=>{
                        console.log("error ", err.response)
                    })
                }
             
            },
            get(){
                axios.get('http://localhost:8000/api/posts').then(res=>{
                    console.log(">> ", res.data)
                    this.posts = res.data
                }).catch(err=>{
                    console.log("error ", err.response)
                })
            },
            edit(id){
                axios.get('http://localhost:8000/api/posts/'+id).then(res=>{
                    console.log("res: ", res.data)
                    this.form.title = res.data.title
                    this.form.body = res.data.body
                    this.idToUpdate = res.data.id
                })
            },
            update(id){
               
            },
            remove(id){
                axios.delete('http://localhost:8000/api/posts/'+id).then(res=>{
                    console.log('deleted', res.data)
                    this.get();
                }).catch(err=>{
                    console.log("error ", err.response)
                })
            }
        },
        created(){
            this.get();
        },
        mounted() {
            console.log('Component mounted.')
        }
    }
</script>
<style lang="scss" scoped>
table,tr,th,td{
    border: 1px solid #ddd;
}
</style>
