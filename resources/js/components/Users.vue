<template>
    <div class="container">
<div class="row">
          <div class="col-12 mt-5">
            <div class="card">
              <div class="card-header">
                <h3 class="card-title">Users Table</h3>

                <div class="card-tools">
                  <!-- <button class="btn btn-success" type="button"  data-toggle="modal" data-target="#addNew">Add New <i class="fas fa-user-plus fa-fw"></i></button> -->
                  <button class="btn btn-success" type="button"  @click="newModal">Add New <i class="fas fa-user-plus fa-fw"></i></button>

                </div>
              </div>
              <!-- /.card-header -->
              <div class="card-body table-responsive p-0">
                <table class="table table-hover">
                  <tbody><tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th>Type</th>
                    <th>Registered At</th>
                    <th>Modify</th>
                  </tr>

                  <tr v-for="user in users" :key="user.id">
                    <td>{{user.id}}</td>
                    <td>{{user.name}}</td>
                    <td>{{user.email}}</td>
                    <td>{{user.type}}</td>
                    <td>{{user.created_at}}</td>
                    <td>
                        <a href="#" @click="editModal(user)">
                          <i class="fa fa-edit blue"></i>
                       
                        </a>
                        <a href="#" @click="deleteUser(user.id)">
                          <i class="fa fa-trash red"></i>

                        </a>

                    </td>
                  </tr>

                </tbody></table>
              </div>
              <!-- /.card-body -->
            </div>
            <!-- /.card -->
          </div>
        </div>


          <!-- Button trigger modal -->
<button type="button" class="btn btn-primary" data-toggle="modal" data-target="#exampleModal">
  Launch demo modal
</button>

<!-- Modal -->
<div class="modal fade" id="addNew" tabindex="-1" role="dialog" aria-labelledby="addNewLabel" aria-hidden="true">
  <div class="modal-dialog modal-dialog-centered" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 v-show="!editmode" class="modal-title" id="exampleModalLabel">Add New</h5>
        <h5 v-show=" editmode" class="modal-title" id="exampleModalLabel">Update User's Info</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>

      <form @submit.prevent="editmode ? updateUser() : CreateUser()">
      <div class="modal-body">

        <div class="form-group">
         <input v-model="form.name" type="text" name="name"
          placeholder="Name" class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
         <has-error :form="form" field="name"></has-error>
         </div>

        <div class="form-group">
         <input v-model="form.email" type="email" name="email" id="email"
          placeholder="Email" class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
         <has-error :form="form" field="email"></has-error>
         </div>

        <div class="form-group">
         <textarea v-model="form.bio"  name="bio" id="bio"
          placeholder="Bio" class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }"></textarea>
         <has-error :form="form" field="bio"></has-error>
         </div>

         <div class="form-group">
          <select name="type" id="type" v-model="form.type" class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
              <option value="">Select user Role </option>
              <option value="">Admin </option>
              <option value="">Standarad User</option>
              <option value="">Author</option>
          </select>
         </div>

        <div class="form-group">
         <input v-model="form.password" type="password" name="password" id="password"
          placeholder="Password" class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
         <has-error :form="form" field="password"></has-error>
         </div>
      </div>
 


      <div class="modal-footer">
        <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
        <button v-show="editmode" type="submit" class="btn btn-success">Update</button>
        <button v-show="!editmode" type="submit" class="btn btn-primary">Create</button>
      </div>
          </form>



    </div>
  </div>
</div>




    </div>
</template>

<script>
    export default {
        data(){
            return{
                editmode:false,//switch between edit and add modal
                users:{
                  

                },

               form:new Form({
                   id:'',
                  name:'',
                  email:'',
                  password:'',
                  type:'',
                  bio:'',
                  photo:''

               })
            }
        },
        methods:{
            updateUser(id){
                this.form.put('api/user/'+this.form.id)
                .then(() =>{
                   $('#addNew').modal('hide');
                 swal(
                 'Udated!',
                 'Information updated.',
                 'success'
                  )
                  Fire.$emit('AfterCreate');
                })
                .catch(() => {

                })

            }, 
              editModal(user){ 
              this.editmode=true;
              
             this.form.reset();   
             $('#addNew').modal('show');
              this.form.fill(user);//in VForm

            },
            newModal(){
              this.editmode=false;  
             this.form.reset();   
             $('#addNew').modal('show');

            },
            deleteUser(id){
  swal({
  title: 'Are you sure?',
  text: "You won't be able to revert this!",
  type: 'warning',
  showCancelButton: true,
  confirmButtonColor: '#3085d6',
  cancelButtonColor: '#d33',
  confirmButtonText: 'Yes, delete it!'
}).then((result) => {
  //send http 
  if(result.value){//toast
   this.form.delete('api/user/'+id)
   .then(() =>{       
         swal(
           'Deleted!',
           'Your file has been deleted.',
           'success'
         )
           Fire.$emit('AfterCreate');
             }).catch(() => {
           }); 
     }

})

            },
            loadUser(){
               axios.get("api/user").then(({data})=>(this.users=data.data));
            },
            CreateUser(){
              this.$Progress.start();

              this.form.post('api/user')
              .then(() =>{
                  this.$Progress.finish();
                  Fire.$emit('AfterCreate');//#2
                  $('#addNew').modal('hide');
                  toast({
                    type: 'success',
                     title: 'user added successfully'
                 })

              }).catch(()=>{

              });


            }

        },
        created() {
            this.loadUser();
            //setInterval(() => {this.loadUser()},3000);
            Fire.$on('AfterCreate',() => {
                this.loadUser();
                });//3
        }
    }
</script>
