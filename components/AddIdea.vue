<template>
  <div>
    <a-button type="primary" @click="showModal">Add Idea</a-button>
    <CreateIdeaForm
      ref="collectionForm"
      :visible="visible"
      @cancel="handleCancel"
      @create="handleCreate"
    />
  </div>
</template>
<script>
import CreateIdeaForm from "./CreateIdeaForm";

export default {
  name: "AddIdea",
  components: {
    CreateIdeaForm
  },
  data() {
    return {
      visible: false
    };
  },
  methods: {
    showModal() {
      this.visible = true;
    },
    handleCancel() {
      this.visible = false;
    },
    handleCreate() {
      const form = this.$refs.collectionForm.form;
      form.validateFields((err, values) => {
        if (err) {
          return;
        }
        this.$emit("add-idea", values);
        form.resetFields();
        this.visible = false;
      });
    }
  }
};
</script>
