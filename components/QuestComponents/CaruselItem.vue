<template>
      <div class="card-carousel-item">
        <div class="upload-img">
          <button class="btn-upload-img">
            <div class="btn-text">Add Image</div>
          </button>
          <TooltipIcon tooltipText="You can upload img" />
        </div>
        <div class="input-custom">
          <Input name="" placeholder="Card title" />
        </div>
        <div class="input-custom">
          <TextArea placeholder="Description (optional)" />
        </div>
      </div>
    <Loader v-if="isLoading" />
  </template>
    
  <script setup>
  </script>
    
  <style lang="scss" scoped>
  * {
    font-family: Basis Grotesque Pro;
    transition: .3s;
  }
  .card-carousel-item {
    padding: 16px;
    width: 100%;
    background: rgba(245, 245, 253, 1);
    margin-bottom: 8px;
    border-radius: 8px;
  }
  
  .upload-img {
    display: flex;
    align-items: center;
    margin-bottom: 8px;
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
  }
  .btn-add-card{
    background-color: rgba(218, 217, 247, 1);
    border-radius: 8px;
    width: 100%;
    padding: 8px 0;
    display: flex;
    justify-content: center;
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
    