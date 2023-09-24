<template>
    <div class="container mt-5">
        <div class="card">
            <div class="card-header">
                <h4>Edit Student</h4>
            </div>
            <div class="card-body">
                <ul class="alert alert-warning" v-if="Object.keys(this.errorList).length > 0">
                    <li class="mb-0 ms-3" v-for="(error,index) in this.errorList" :key="index">
                        {{ error[0] }}
                    </li>
                </ul>
                <div class="mb-3">
                    <label for="">Alamat</label>
                    <input type="text" v-model="model.student.alamat" class="form-control"/>
                </div>
                <div class="mb-3">
                    <label for="">Bulan</label>
                    <input type="text" v-model="model.student.bulan" class="form-control"/>
                </div>
                <div class="mb-3">
                    <label for="">Telepon</label>
                    <input type="text" v-model="model.student.phone" class="form-control"/>
                </div>
                <div class="mb-3">
                    <label for="">Catatan</label>
                    <input type="text" v-model="model.student.catatan" class="form-control"/>
                </div>
                <div class="mb-3">
                    <button type="button" @click="updateStudent" class="btn btn-primary">Update</button>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import axios from 'axios'
    export default {
        name:'createStudent',
        data(){
            return{
                id : "", 
                errorList : 0,
                model:{
                    student:{
                        alamat:"",
                        bulan:"",
                        phone:"",
                        catatan:""
                    }
                }
            }
        },
        mounted(){
            // console.log(this.$route.params.id)
            this.id = this.$route.params.id;
            this.getStudentData(this.$route.params.id);
        },
        methods:{
            getStudentData(id){
                axios.get(`http://localhost:8000/user/${id}`).then(res =>{
                    console.log(res.data);
                    this.model.student = res.data
                })
                .catch(function (error) {
                        if (error.response) {
                            if(error.response.status ==404){
                                alert("data tidak ditemukan!");
                              }
                            } 
                    })
            },
            updateStudent(){
                var $this = this;
                axios.put(`http://localhost:8000/user/${this.id}`,this.model.student)
                     .then(res =>{
                        console.log(res)
                        alert("sukses di update");
                        this.model.student = {
                            alamat:"",
                            bulan:"",
                            phone:"",
                            catatan:""
                        }
                        this.errorList = "";
                        this.$router.push('/students');
                     })
                     .catch(function(error){
                        if(error.response){
                            
                        }
                     })
                     .catch(function (error) {
                        if (error.response) {
                            if(error.response.status ==422){
                                myThis.errorList = error.response.data.errors;
                            }
                            // console.log(error.response.data);
                            // console.log(error.response.status);
                            // console.log(error.response.headers);
                            } else if (error.request) {
                            console.log(error.request);
                            } else {
                            console.log('Error', error.message);
                            }
                            console.log(error.config);
                    });
            }
        } 
    }
</script>