<template>
  <div>
    <a-modal
      :visible="visible"
      title="Add your Idea"
      ok-text="Create"
      @cancel="
        () => {
          $emit('cancel');
        }
      "
      @ok="
        () => {
          $emit('create');
        }
      "
    >
      <a-form layout="vertical" :form="form">
        <a-form-item label="Idea Title">
          <a-input
            ref="titleRef"
            v-decorator="[
              'title',
              {
                rules: [
                  {
                    required: true,
                    message: 'Please input the title of your idea'
                  }
                ]
              }
            ]"
          />
        </a-form-item>
        <a-form-item label="Idea Description">
          <a-input
            v-decorator="[
              'body',
              {
                rules: [
                  {
                    required: true,
                    message: 'Please add a short description of your idea'
                  },
                  {
                    message: 'Maximum of 140 characters allowed',
                    max: 5
                  }
                ]
              }
            ]"
            type="textarea"
          />
        </a-form-item>
      </a-form>
    </a-modal>
  </div>
</template>

<script>
export default {
  name: "CreateIdeaForm",
  props: ["visible"],
  watch: {
    visible(value) {
      if (value) {
        this.focusTitle();
      }
    }
  },
  beforeCreate() {
    this.form = this.$form.createForm(this, {
      name: "form_in_modal"
    });
  },
  mounted() {},
  methods: {
    focusTitle() {
      setTimeout(() => {
        this.$refs.titleRef.$el.focus();
      }, 100);
    }
  }
};
</script>

<style></style>
