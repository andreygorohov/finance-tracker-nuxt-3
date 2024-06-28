<template>
  <Ucard>
    <template #header> Confirm your email </template>
    <div class="text-center">
      <p class="mb-4">
        We have sent an email to <strong>{{ email }}</strong> with a link to
        sign-in.
      </p>
      <p><strong>Important:</strong> The link will expire in 5 minutes.</p>
    </div>
  </Ucard>
</template>

// after auth
<script setup>
useRedirectIfAuthenticated();
</script>
