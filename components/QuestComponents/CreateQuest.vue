<template>
  <div class="card-carousel">
    <div class="title-carousel">Carousel</div>
    <div class="subtitle-carousel">Create 2 to 8 carousel cards.</div>

    <TransitionGroup name="list" tag="ul" appear>
      <div v-for="(item) in cardItems" :key="item.id" class="card-carousel-item" transition="fade">

        <button class="btn-delete" @click="deleteCardItem(item.id)">Delete Card</button>
        <!-- <UploaderButton
          @changeError="handleImageError"
          :imagesFiles="images"
          @images="images = $event"
        /> -->
        
        <!-- <ul v-if="images.length">
          <li v-for="(image, index) in images" :key="index">
            <img :src="fileUrl(image)" alt="Selected Image" style="max-width: 200px; max-height: 200px;" />
          </li>
        </ul> -->
        <div class="upload-img">
          <button class="btn-upload-img">
            <button class="btn-text">
              Add Image
            </button>
              <Icon name="Add" :size="20" />
          </button>
          <TooltipIcon tooltipText="Add an image if necessary. Recommended size - 320 x 320 px. PNG, JPEG, PDF. Maximum 10 MB." />
        </div>
        <div class="input-custom">
          <Input v-model="item.title" :name="''" :placeholder="'Card title'" />
        </div>
        <div class="input-custom">
          <TextArea v-model="item.description" :placeholder="'Description (optional)'" />
        </div>

      </div>
      
      
      <button class="btn-add-card" @click="addCardItem">
        <div class="btn-text">Add Card</div>
        <Icon name="Add" :size="20" />
      </button>
    </TransitionGroup>
  </div>
  <Loader v-if="isLoading" />
</template>
  
<script setup>
const cardItems = ref([
  { id: `${Date.now()}`, title: '', description: '' }
]);
const images = ref([]);
console.log(images.length);

function addCardItem() {
  const newItem = { id: `${Date.now()}`, title: '', description: '' };
  cardItems.value.push(newItem);
}

function deleteCardItem(itemId) {
  const index = cardItems.value.findIndex(item => item.id === itemId);
  if (index !== -1) {
    cardItems.value.splice(index, 1)
  }
}

function handleImageError(error) {
  // Handle image upload error if needed
  console.error('Image upload error:', error);
}

function handleImagesSelection(selectedImages) {
  // Update the list of selected images
  images.value = selectedImages;
}
function fileUrl(file) {
  if (file instanceof Blob) {
    return URL.createObjectURL(file);
  } else {
    console.error('Invalid file object:', file);
    return '';
  }
}
</script>
  
<style lang="scss" scoped>
* {
  font-family: Basis Grotesque Pro;
  transition: .3s;
}

.list-move,   
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}.list-leave-active {
  position: absolute;
}

.list-leave-active {
  position: absolute;
}

.card-carousel {
  width: 100%;
  padding: 16px;

}
.title-carousel{
  font-size: 20px;
  color: rgba(106, 109, 143, 1);
}
.subtitle-carousel{
  font-size: 12px;
  color: rgba(106, 109, 143, 1);
  margin-bottom: 24px;
}
.btn-upload-img{
  position: relative;
}
.opacity-uploader{
  position: absolute;
  width: 100%;
  height: 100%;
  overflow: hidden;
  top: 0;
  left: 0;
  z-index: 10;
  opacity: 0;
}
.card-carousel-item {
  padding: 16px;
  width: 100%;
  position: relative;
  // background: rgba(245, 245, 253, 1);
  background-color: rgb(218 217 247 / 22%);
  margin-bottom: 8px;
  border-radius: 8px;
}

.upload-img {
  display: flex;
  align-items: center;
  margin-bottom: 8px;
}

.btn-delete {
  position: absolute;
  top: 16px;
  right: 16px;
  padding: 7px 12px;
  color: rgba(106, 109, 143, 1);
  font-weight: 500;
  font-size: 12px;
  letter-spacing: 1.4%;
  border-radius: 6px;

  &:hover {
    background-color: rgba(234, 234, 251, 1);
  }
}

.btn-upload-img {
  background-color: rgba(218, 217, 247, 1);
  padding: 8px 12px;
  border-radius: 6px;
  margin-right: 8px;

  &:hover {
    background-color: rgba(56, 64, 91, 1);

    .btn-text {
      color: rgba(255, 255, 255, 1);
    }
  }
}

.btn-text {
  color: rgba(56, 64, 91, 1);
  font-size: 12px;
  font-weight: 500;
  margin-right: 10px;
}

.btn-add-card {
  background-color: rgba(218, 217, 247, 1);
  border-radius: 8px;
  width: 100%;
  padding: 8px 0;
  display: flex;
  justify-content: center;
  align-items: end;
  &:hover {
    background-color: rgba(56, 64, 91, 1);

    .btn-text {
      color: rgba(255, 255, 255, 1);
    }
  }
}

.input-custom {
  margin-bottom: 16px;
}
</style>
  ../OnChain/utils.оы
  