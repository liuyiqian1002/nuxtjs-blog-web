<template>
  <div class="response-wrap footer">
    <div class="site">
      <span>© 个人博客 - 千千客栈 - qqinns.com</span>
      <span>liuyiqian1002@gmail.com</span>
    </div>
    <div class="copyright">
      <a
        class="copyright-link"
        href="https://beian.miit.gov.cn"
        target="_blank"
      >
        粤ICP备2020120493号
      </a>
    </div>
  </div>
</template>

<script>
export default {
  name: 'FooterIndex',
}
</script>

<style scoped lang="scss">
.footer {
  position: absolute;
  bottom: 0;
  display: flex;
  justify-content: space-between;
  margin: 10px auto 6px;
  left: 50%;
  transform: translateX(-50%);
}
.site {
  font-size: 14px;
  color: #666666;
}
.site span {
  margin-right: 24px;
}
.copyright {
  &-link {
    font-size: 14px;
    color: #666666;
    text-decoration: none;
  }
}

@media screen and (max-width: 540px) {
  .footer {
    flex-direction: column;
    margin: 32px auto;
  }
  .copyright {
    margin-top: 10px;
  }
}
</style>
