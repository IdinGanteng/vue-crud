<template>
    <main>
        <div class="container text-center" style="margin-top: 50px;">
            <div class="card">
                <div class="card-header d-flex justify-content-between align-items-center">
                    <h4>
                        Students
                    </h4>
                    <input class="form-control w-25" v-model="searchKeyword" type="search" placeholder="Search" aria-label="Search">
                    <RouterLink to="/students/create" class="btn btn-primary float-end">
                        Add Student
                    </RouterLink>
                </div>
                <div class="card-body">
                    <div class="table-responsive">
                    <table class="table table-bordered">
                        <tr>
                            <th>ID</th>
                            <th>Nama</th>
                            <th>Alamat</th>
                            <th>Bulan</th>
                            <th>Telepon</th>
                            <th>Catatan</th>
                            <th>Action</th>
                        </tr>
                        <tbody v-if="this.filteredStudents.length > 0">
                            <tr v-for="(student, id) in filteredStudents" :key="id">
                                <td>{{student.id}}</td>
                                <td>{{ student.nama }}</td>
                                <td>{{ student.alamat }}</td>
                                <td>{{ student.bulan }}</td>
                                <td>{{ student.phone }}</td>
                                <td>{{ student.catatan }}</td>
                                <td>
                                    <RouterLink :to="{path:'/students/'+student.id+'/edit'}" class="btn btn-success btn-sm mx-3">
                                        Edit
                                    </RouterLink>
                                    <button type="button" @click="deleteStudent(student.id)" class="btn btn-danger btn-sm">
                                        Delete
                                    </button>
                                </td>
                            </tr>
                        </tbody>
                        <tbody v-else>
                            <tr>
                                <td colspan="6">
                                    <img src="../../assets/empty.png"/>
                                    <h4>Data Kosong</h4>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                    </div>
                </div>
            </div>
        </div>
    </main>
</template>
<script>
import axios from 'axios'
export default{
    name:'student',
    data(){
        return{
            students:[],
            searchKeyword:''
        }
    },
    mounted(){
        // console.log('kontol')
        this.getStudents();
    },
    computed: {
    filteredStudents() {
        const keyword = this.searchKeyword.toLowerCase();
        return this.students.filter(student => {
            return (
                student.nama.toLowerCase().includes(keyword) ||
                student.alamat.toLowerCase().includes(keyword) ||
                student.bulan.toLowerCase().includes(keyword) ||
                student.phone.toLowerCase().includes(keyword) ||
                student.catatan.toLowerCase().includes(keyword)
            );
        });
    }
},
    methods:{
        getStudents(){
            axios.get('http://localhost:8000/user').then(res =>{
                this.students = res.data
                console.log(this.students)
            })
        },
        deleteStudent(id){
            if(confirm('Apakah anda yakin ingin menghapus data ini?')){
                axios.delete(`http://localhost:8000/user/${id}`)
                     .then(res =>{
                        alert("data sukses dihapus");
                        this.getStudents();
                     })
            }
        }
    }
}
</script>