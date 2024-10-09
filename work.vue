const mutObserver = async () => {
  let observer = new MutationObserver(function (mutations) {
    console.log(mutations);

    mutations.forEach(function (mutationRecord) {
      const wtBodyElement = document.getElementById('wt-body');
      
      if (wtBodyElement && mutationRecord.target.style.paddingLeft) {
        wtBodyElement.style.paddingLeft = mutationRecord.target.style.paddingLeft;
      }
    });
  });

  const pageContainer = document.getElementsByClassName('q-page-container')[0];
  
  if (pageContainer) {
    observer.observe(pageContainer, { attributes: true, attributeFilter: ['style'] });
  } else {
    console.error("Не найден элемент с классом 'q-page-container'");
  }
};

