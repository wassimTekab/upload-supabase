<template>
  <div>
    <form @submit.prevent="onUpload">
      <div class="form-group">
        <input type="file" name="imagesArray" multiple @change="onChange" />
      </div>
      <div class="form-group">
        <button class="btn btn-success">Store</button>
      </div>
    </form>
  </div>
</template>

<script>
import { ref } from "vue";
import { supabase } from "../supabaseClient";
export default {
  name: "multiple",
  setup() {
    const filesArray = ref([]);
    const filesUrls = ref([]);
    const uploading = ref(false);

    const onChange = (event) => {
      filesArray.value = [...filesArray.value, event.target.files[0]];

      console.log(filesArray.value);
    };

    const onUpload = async () => {
      uploading.value = true;
      //  if (!event.target.files || event.target.files.length === 0) {
      //   throw new Error("You must select an image to upload.");
      // }

      const filesExt = filesArray.value.map((file) =>
        file.name.split(".").pop()
      );
      console.log({ filesExt });
      const filesName = filesExt.map(
        (fileExt) => `${Math.random()}.${fileExt}`
      );

      console.log(filesName);
      const filesPath = filesName.map((fileName) => `files/${fileName}`);
      console.log(filesPath);
      filesArray.value.forEach(async (file, index) => {
        try {
          let { error: uploadError } = await supabase.storage
            .from("avatars")
            .upload(filesPath[index], file);
          console.log("file n", index);
          if (uploadError) {
            throw uploadError;
          }
        } catch (error) {
          alert(error.message);
        }
      });
      uploading.value = false;
       downloadFiles() 
    };

    const downloadFiles = async () => {
      const { data, error } = await supabase.storage
        .from("avatars")
        .list("files", {
          limit: 100,
          offset: 0,
          sortBy: { column: "name", order: "asc" },
        });
        if (error) {
            throw error;
        }
        
        console.log("files",data)
        filesUrls.value = data.map(file => file.name)
        console.log("filesUrls",filesUrls.value)
    };

    console.log("filesUrls",filesUrls.value)


    return {
      onChange,
      onUpload,
      downloadFiles
    };
  },
};
</script>

<style scoped lang="css">
.container {
  max-width: 550px;
}
</style>
