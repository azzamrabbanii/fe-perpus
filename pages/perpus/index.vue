<template>
    <div>
        <div class="container">
            <b-table class="mt-5" striped hover :items="items" :fields="fields">
                <template #cell(#)="data">
                    <button class="btn btn-success" @click="onEditForm(data.item)">
                        Edit
                    </button>
                    <button class="btn btn-danger" @click="onDelete(data.item)">
                        Delete
                    </button>
                </template>
            </b-table>
        </div>
        <div class="container">
            <b-form @submit="onSubmit" @reset="onReset">
                <b-form-group id="input-group-1" label="Your Name:" label-for="input-1">
                    <b-form-input id="input-1" v-model="form.namaPeminjam" placeholder="nama peminjam" required>
                    </b-form-input>
                </b-form-group>
                <b-form-group id="input-group-2" label="Book Name:" label-for="input-2">
                    <b-form-input id="input-2" v-model="form.namaBuku" placeholder="nama buku" required>
                    </b-form-input>
                </b-form-group>
                <b-form-group id="input-group-2" label="Date:" label-for="input-2">
                        <b-form-input id="input-2" v-model="form.tanggalPinjam" placeholder="tanggal pinjam" required>
                        </b-form-input>
                </b-form-group>
                <b-button type="submit" variant="primary">Submit</b-button>
                <b-button type="reset" variant="danger">Reset</b-button>
            </b-form>
            <div v-if="onEdit" class="mt-5">
                <b-form @submit="onSubmitEdit">
                    <b-form-group id="input-group-1" label="nama:" label-for="input-1">
                        <b-form-input v-model="form_edit.namaPeminjam" placeholder="nama peminjam" required>
                        </b-form-input>
                    </b-form-group>
                    <b-form-group id="input-group-2" label="nama buku:" label-for="input-2">
                        <b-form-input v-model="form_edit.namaBuku" placeholder="nama buku" required>
                        </b-form-input>
                    </b-form-group>
                    <b-form-group id="input-group-2" label="Date:" label-for="input-2">
                            <b-form-input id="input-2" v-model="form_edit.tanggalPinjam" placeholder="tanggal pinjam" required>
                            </b-form-input>
                    </b-form-group>
                    <b-button type="submit" variant="primary">Submit</b-button>
                </b-form>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            form: {
                namaPeminjam: "",
                namaBuku: "",
                tanggalPinjam: "",
            },
            form_edit: {
                namaPeminjam: "",
                namaBuku: "",
                tanggalPinjam: "",
            },

            show: true,
            onEdit: false,
            fields: [{
                key: "id",
                sortable: true
            },
            {
                key: "namaPeminjam",
                sortable: true
            },
            {
                key: "namaBuku",
                sortable: true
            },
            {
                key: "tanggalPinjam",
                sortable: true
            },
            {
                key: "#",
                sortable: false
            }
            ],
            items: [],
        };
    },
    methods: {
        async onDelete(data) {
            await this.$axios.$delete(
                `http://localhost:35000/users/perpus/` + data.id
            );
            this.getapi();
        },
        async onEditForm(data) {
            this.onEdit = true;
            this.form_edit.namaPeminjam = data.namaPeminjam;
            this.form_edit.namaBuku = data.namaBuku;
            this.form_edit.tanggalPinjam = data.tanggalPinjam;
            this.form_edit.id = data.id;
        },
        async getapi() {
            const data = await this.$axios.$get(
                "http://localhost:35000/users/perpus",
            );
            this.items = data.data;
            for (let i = 0; i < this.items.length; i++) {
                const element = this.items[i]
                element.id = i + 1
            }
            console.log(data.data);
        },
        async onSubmitEdit(event) {
            event.preventDefault();
            try {
                await this.$axios.$put(
                    "http://localhost:35000/users/perpus/" + this.form_edit.id,
                    this.form_edit
                );
                this.getapi();
            }
            catch (error) {
                console.log(error);
            }
        },

        async onSubmit(event) {
            event.preventDefault();
            try {
                await this.$axios.$post(
                    "http://localhost:35000/users/perpus",
                    this.form
                );
                this.getapi();
            } catch (error) {
                console.log(error);
            }
        },
        onReset(event) {
            event.preventDefault();
            this.form.namaPeminjam = "";
            this.form.namaBuku = "";
            this.form.tanggalPinjam = "";
            this.show = false;
            this.$nextTick(() => {
                this.show = true;
            });
        },
    },
    mounted() {
        this.getapi();
    }
}

</script>