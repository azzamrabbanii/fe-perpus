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
          <b-form-input id="input-1" v-model="form.name" placeholder="Enter name" required>
          </b-form-input>
        </b-form-group>
        <b-form-group id="input-group-2" label="Your Name:" label-for="input-2">
          <b-form-input id="input-2" v-model="form.age" placeholder="Enter umur" required>
          </b-form-input>
        </b-form-group>
        <b-button type="submit" variant="primary">Submit</b-button>
        <b-button type="reset" variant="danger">Reset</b-button>
      </b-form>
      <div v-if="onEdit" class="mt-5">
        <b-form @submit="onSubmitEdit">
          <b-form-group id="input-group-1" label="Name:" label-for="input-1">
            <b-form-input v-model="form_edit.name" placeholder="Enter Nama" required>
            </b-form-input>
          </b-form-group>
          <b-form-group id="input-group-2" label="Age:" label-for="input-2">
            <b-form-input v-model="form_edit.age" placeholder="Enter Umur" required>
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
        name: "",
        age: "",
      },
      form_edit: {
        name: "",
        age: "",
      },

      show: true,
      onEdit: false,
      fields: [{
        key: "id",
        sortable: true
      },
      {
        key: "name",
        sortable: true
      },
      {
        key: "age",
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
        `http://localhost:35000/users/employee/` + data.id
      );
      this.getapi();
    },
    async onEditForm(data) {
      this.onEdit = true;
      this.form_edit.name = data.name;
      this.form_edit.age = data.age;
      this.form_edit.id = data.id;
    },
    async getapi() {
      const data = await this.$axios.$get(
        "http://localhost:35000/users/employee",
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
          `http://localhost:35000/users/employee/` + this.form_edit.id,
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
          "http://localhost:35000/users/employee",
          this.form
        );
        this.getapi();
      } catch (error) {
        console.log(error);
      }
    },
    onReset(event) {
      event.preventDefault();
      this.form.name = "";
      this.form.age = "";
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