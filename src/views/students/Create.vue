<template>
    <div class="container mt-5">
        <div class="card">
            <div class="card-header">
                <h4>Add Student</h4>
            </div>
            <div class="card-body">
                <ul class="alert alert-warning" v-if="Object.keys(this.errorList).length > 0">
                    <li class="mb-0 ms-3" v-for="(error,index) in this.errorList" :key="index">
                        {{ error[0] }}
                    </li>
                </ul>
                <div class="mb-3">
                    <label for="">Nama</label>
                    <input type="text" v-model="model.student.nama" class="form-control"/>
                </div>
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
                    <button type="button" @click="saveStudent" class="btn btn-primary">Save</button>
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
                errorList : 0,
                model:{
                    student:{
                        nama:"",
                        alamat:"",
                        bulan:"",
                        phone:"",
                        catatan:""
                    }
                }
            }
        },
        methods:{
            saveStudent(){
                var $this = this;
                axios.post('http://localhost:8000/user',this.model.student)
                     .then(res =>{
                        console.log(res)
                        alert("sukses ditambahkan");
                        this.model.student = {
                            nama:"",
                            alamat:"",
                            bulan:"",
                            phone:"",
                            catatan:""
                        }
                        this.errorList = "";
                        this.$router.push('/students')
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