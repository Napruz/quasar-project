this.$router.push(path).catch(err => {
  if (err.name !== 'NavigationDuplicated') {
    throw err;
  }
});


this.$router.push(path).then(() => {
  this.$router.go(0);
});

