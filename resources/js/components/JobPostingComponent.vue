<template>
    <div>
        <h1>Job Posting Lists</h1>
        <table class="table table-hover">
            <thead>
                <th>Job Name</th>
                <th>Job Description</th>
                <th>Vacants</th>
                <th>Actions</th>
            </thead>
            <tbody>
                <tr v-for="(item, index) in postingList" :key="item.id">
                    <td>{{ item.job_name }}</td>
                    <td>{{ item.job_description }}</td>
                    <td>{{ item.vacants }}</td>
                    <td>
                        <button class="btn btn-secondary btn-sm" @click="edit(item)"><i class="fas fa-edit"></i></button>
                        <button class="btn btn-danger btn-sm" @click="remove(item,index)"><i class="fas fa-trash"></i></button>
                    </td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <td>
                        <div class="form-group">
                            <input type="text" class="form-control" placeholder="Input Job Name" required v-model="form.job_name">
                        </div>
                    </td>
                    <td>
                        <div class="form-group">
                            <input type="text" class="form-control" placeholder="Input Job Description" required v-model="form.job_description">
                        </div>
                    </td>
                    <td>
                        <div class="form-group">
                            <input type="number" class="form-control" placeholder="Input Vacants" required v-model="form.vacants">
                        </div>
                    </td>
                    <td>
                        <div class="form-group">
                            <button class="btn btn-primary btn-sm" @click="submit"><i class="fas fa-plus-square"></i></button>
                        </div>
                    </td>
                </tr>
            </tfoot>
        </table>
        <div class="modal fade" id="editModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalCenterTitle" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLongTitle">Modal title</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">
                    <div class="form-group">
                        <label for="">Job Name</label>
                        <input type="text" class="form-control" placeholder="Input Job Name" v-model="formEdit.job_name">
                    </div>
                    <div class="form-group">
                        <label for="">Job Description</label>
                        <input type="text" class="form-control" placeholder="Input Job Name" v-model="formEdit.job_description">
                    </div>
                    <div class="form-group">
                        <label for="">Vacant</label>
                        <input type="text" class="form-control" placeholder="Input Job Name" v-model="formEdit.vacants">
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                    <button type="button" class="btn btn-primary" @click="save()">Save changes</button>
                </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: ['posting'],
    data () {
        return {
            postingList: this.posting,
            form: {
                job_name: null,
                job_description: null,
                vacants: 0
            },
            formEdit: {
                job_name: null,
                job_description: null,
                vacants: 0
            },
            selectedId: null
        }
    },
    methods: {
        submit() {
            const vm = this;
            axios.post('/job_postings', this.form)
            .then(function (response) {
                vm.postingList.push(response.data.data);
                vm.form.job_name = null
                vm.form.job_description = null
                vm.form.vacants = 0
            })
            .catch(function (error) {
                alert(error);
                console.log(error)
            });
        },
        remove(item,index) {
            const vm = this;
            axios.delete(`/job_postings/${item.id}`)
            .then(function (response) {
                vm.postingList.splice(index,1);
            })
            .catch(function (error) {
                alert("Something went wrong!");
                console.log(error)
            });
        },
        edit(item) {
            const vm = this;
            vm.formEdit.job_name = item.job_name;
            vm.formEdit.job_description = item.job_description;
            vm.formEdit.vacants = item.vacants;
            vm.selectedId = item.id;
            $('#editModal').modal('show');
        },
        save() {
            const vm = this;
            axios.put(`/job_postings/${vm.selectedId}`, this.formEdit)
            .then(function (response) {
                alert('Job Posting has been Updated!')
                location.reload();
            })
            .catch(function (error) {
                alert("Something went wrong!");
                console.log(error)
            });
        }
    }
}
</script>