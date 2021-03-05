<template>
    <div class="container">
        <div div class="row mt-5">
            <div class="col-md-12">
                <div class="card">
                    <div class="card-header">
                        <h3 class="card-title">User Table</h3>
                        <div class="card-tools">
                            <button
                                class="btn btn-success"
                                @click="newModal"
                            >
                                Add New
                                <i class="fas fa-user-plus fa-fw"></i>
                            </button>
                        </div>
                    </div>
                    <!-- /.card-header -->
                    <div class="card-body table-responsive p-0">
                        <table class="table table-hover">
                            <tbody>
                                <tr>
                                    <th>ID</th>
                                    <th>Name</th>
                                    <th>e-mail</th>
                                    <th>Type</th>
                                    <th>Registered at</th>
                                    <th>Modify</th>
                                </tr>

                                <tr v-for="user in users" :key="user.id">
                                    <td>{{ user.id }}</td>
                                    <td>{{ user.name }}</td>
                                    <td>{{ user.email }}</td>
                                    <td>{{ user.type | upText }}</td>
                                    <td>{{ user.created_at | myDate }}</td>
                                    <td>
                                        <a href="#" @click="editModal(user)"
                                            ><i class="fa fa-edit blue"></i
                                        ></a>
                                        /
                                        <a href="#" @click="deleteUser(user.id)"
                                            ><i class="fa fa-trash red"></i
                                        ></a>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                    <!-- /.card-body -->
                </div>
                <!-- /.card -->
            </div>
        </div>

        <!-- Modal -->
        <div
            class="modal fade"
            id="addNew"
            tabindex="-1"
            role="dialog"
            aria-labelledby="addNewLabel"
            aria-hidden="true"
        >
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 v-show="!editmode" class="modal-title" id="addNewLabel">
                            Add New
                        </h5>
                        <h5 v-show="editmode" class="modal-title" id="addNewLabel">
                            Update User's Info
                        </h5>
                        <button
                            type="button"
                            class="close"
                            data-dismiss="modal"
                            aria-label="Close"
                        >
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>

                    <form @submit.prevent="editmode ? updateUser() : createUser()">
                        <div class="modal-body">
                            <div class="form-group">
                                <input
                                    v-model="form.name"
                                    type="text"
                                    name="name"
                                    placeholder="name"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has('name')
                                    }"
                                />
                                <has-error
                                    :form="form"
                                    field="name"
                                ></has-error>
                            </div>

                            <div class="form-group">
                                <input
                                    v-model="form.email"
                                    type="text"
                                    name="email"
                                    placeholder="e-mail address"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has('email')
                                    }"
                                />
                                <has-error
                                    :form="form"
                                    field="email"
                                ></has-error>
                            </div>

                            <div class="form-group">
                                <input
                                    v-model="form.bio"
                                    type="text"
                                    name="bio"
                                    placeholder="short bio for user (optional)"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has('bio')
                                    }"
                                />
                                <has-error :form="form" field="bio"></has-error>
                            </div>

                            <div class="form-group">
                                <select
                                    v-model="form.type"
                                    type="text"
                                    name="type"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has('type')
                                    }"
                                >
                                    <option value="">Select user role</option>
                                    <option value="admin">Admin</option>
                                    <option value="user">Standard User</option>
                                    <option value="author">Author</option>
                                </select>
                                <has-error
                                    :form="form"
                                    field="type"
                                ></has-error>
                            </div>

                            <div class="form-group">
                                <input
                                    v-model="form.password"
                                    type="password"
                                    name="password"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has(
                                            'password'
                                        )
                                    }"
                                />
                                <has-error
                                    :form="form"
                                    field="password"
                                ></has-error>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button
                                type="button"
                                class="btn btn-danger"
                                data-dismiss="modal"
                            >
                                Close
                            </button>
                            <button v-show="editmode" type="submit" class="btn btn-success">
                                Update
                            </button>
                            <button v-show="!editmode" type="submit" class="btn btn-primary">
                                Create
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            editmode: false,
            users: {},
            form: new Form({
                id: "",
                name: "",
                email: "",
                password: "",
                type: "",
                bio: "",
                photo: ""
            })
        };
    },

    methods: {
        updateUser(){
            this.$Progress.start();
            // console.log("editing data");
            this.form.put('api/user/'+this.form.id)
            .then(()=>{
                $('#addNew').modal('hide');
                Swal.fire(
                    'Updated',
                    'Information has been updated',
                    'success'
                )
                this.$Progress.finish();
                reload.$emit("afterCreate");
            })
            .catch(()=>{
                this.$Progress.fail();
            });
        },
        editModal(user){
            this.editmode=true;
            this.form.reset();
            $('#addNew').modal('show');
            this.form.fill(user);
        },
        newModal(){
            this.editmode=false;
            this.form.reset();
            $('#addNew').modal('show');
        },
        deleteUser(id){
            Swal.fire({
                title: 'Are you sure?',
                text: "You won't be able to revert this!",
                icon: 'warning',
                showCancelButton: true,
                confirmButtonColor: '#3085d6',
                cancelButtonColor: '#d33',
                confirmButtonText: 'Yes, delete it!'
            }).then((result) => {

                //send request to the server
                if (result.isConfirmed) {
                    this.form.delete('api/user/'+id).then(()=>{
                            Swal.fire(
                                'Deleted!',
                                'Your file has been deleted.',
                                'success'
                            )
                        reload.$emit("afterCreate");
                    }).catch(()=>{
                        swal("Failed!","There was something wrong","warning");
                    });
                }
            })
        },
        loadUsers() {
            axios.get("api/user").then(({ data }) => (this.users = data.data));
        },

        createUser() {
            this.$Progress.start();
            this.form.post("api/user")
            .then(()=>{
                reload.$emit("afterCreate");
                $("#addNew").modal("hide")

                toast.fire({
                    icon: "success",
                    title: "Created successfully"
                })
                this.$Progress.finish();
            })
            .catch(()=>{

            })
        }
    },
    created() {
        this.loadUsers();
<<<<<<< HEAD
        reload.$on("afterCreate", () => {
            this.loadUsers();
        });
=======
        Fire.$on('afterCreate', () => {
            this.loadUsers();
        }
        );
>>>>>>> ee4545f2b523970c12ed88e16b9364b33bde6848
        // setInterval(() => this.loadUsers(), 3000);
    }
}
</script>
