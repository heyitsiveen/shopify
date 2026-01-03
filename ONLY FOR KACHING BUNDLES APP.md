## Requirements
1. Add this styles to re-design the `Kaching Bundles`

1st Design
```css
/*! KACHING BUNDLES  */
.kaching-bundles__block-title {
  display: none !important;
}
.kaching-bundles__bars {
  display: grid !important;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 1rem;
  justify-content: center; /* Helps centering in general */
}

/* === Special alignment rules === */

/* Case: 2 options → center them in columns 2 and 3 */
.kaching-bundles__bar:first-child:nth-last-child(2),
.kaching-bundles__bar:first-child:nth-last-child(2) ~ .kaching-bundles__bar {
  grid-column: span 1;
  justify-self: center;
}
.kaching-bundles__bar:first-child:nth-last-child(2) {
  grid-column: 2; /* first goes in the middle-left */
}
.kaching-bundles__bar:first-child:nth-last-child(2) ~ .kaching-bundles__bar {
  grid-column: 3; /* second goes in the middle-right */
}

/* Case: 4 options → 4th item should be centered */
.kaching-bundles__bar:nth-child(4):last-child {
  grid-column: 2 / span 1;
}

/* Case: 5 options → last 2 items should be centered */
.kaching-bundles__bar:nth-child(4):nth-last-child(2),
.kaching-bundles__bar:nth-child(5):last-child {
  justify-self: center;
}

.kaching-bundles__bar {
  position: relative !important;
  margin: 0 !important;
}

.kaching-bundles__bar input[type="radio"] {
  display: none !important;
}

/* Card styling */
.kaching-bundles__bar-container {
  position: relative !important;
  display: flex !important;
  height: 100% !important;
  flex-direction: column !important;
  background: white !important;
  border-radius: var(--button-border-radius) !important;
  border: 1px solid var(--color-border) !important;
  padding-block: 1.5rem !important;
  padding-inline: 1rem !important;
  text-align: center !important;
  cursor: pointer !important;
  transition: all 0.2s ease !important;
  box-shadow: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1) !important;
}

.kaching-bundles__bar-container:hover {
  background-color: #e6e6e6 !important;
  box-shadow: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1) !important;
}

.kaching-bundles__bar--selected .kaching-bundles__bar-container {
  border: 1px solid #111111 !important;
}

/* "Most Popular" badge styling */

.kaching-bundles .kaching-bundles__bar-most-popular__content {
  margin: 0 6px !important;
  padding: 6px 8px;
  border-bottom-left-radius: 5px;
  border-bottom-right-radius: 5px;
  font-family: sans-serif;
  font-size: 12px;
  font-weight: 700;
  line-height: 13px;
  color: var(--bar-most-popular-color, #fff);
  background-color: var(--bar-most-popular-background-color);
  left: -3.5rem !important;
  right: 0.5rem !important;
  background: linear-gradient(#90502b, #2a170d) !important;
  padding: 5px 4px !important;
  border-radius: 0.25rem !important;
  font-size: 0.625rem !important;
  font-weight: 600 !important;
  text-transform: uppercase !important;
  min-width: 109px;
}

.kaching-bundles
  .kaching-bundles__bar-most-popular.kaching-bundles__bar-most-popular--simple {
  top: -14px !important;
  left: 50% !important;
  right: auto !important; /* Override previous right */
  transform: translateX(-50%) !important;
  /* Optional: if you also want vertical centering, adjust top */
  /* top: 50% !important; transform: translate(-50%, -50%); */
}

.kaching-bundles .kaching-bundles__bar-most-popular__content:before,
.kaching-bundles .kaching-bundles__bar-most-popular__content:after {
  display: none !important;
}

/* "Best Deal" badge styling 
.kaching-bundles__bar:nth-child(3) .kaching-bundles__bar-container::before {
  content: "BEST DEAL" !important;
  position: absolute !important;
  top: -10px !important;
  left: 0.5rem !important;
  right: 0.5rem !important;
  background: #E05403 !important;
  color: white !important;
  padding: 0.25rem 0.75rem !important;
  border-radius: 0.25rem !important;
  font-size: 0.625rem !important;
  font-weight: 600 !important;
  text-transform: uppercase !important;
}
  */

.kaching-bundles__bar-wrapper {
  flex: 1 !important;
  display: flex !important;
  flex-direction: column !important;
  padding: 0 !important;
}

.kaching-bundles__bar-main {
  flex: 1 !important;
  display: flex !important;
  flex-direction: column !important;
  margin: 0 !important;
}

.kaching-bundles__bar-radio {
  display: none !important;
}

.kaching-bundles__bar-content {
  flex: 1 !important;
  display: flex !important;
  flex-direction: column !important;
  justify-content: space-between !important;
}

/* To center titles and subtitles using "Vertical Style" */
.kaching-bundles__bar-content-left {
  align-items: center !important;
}

.kaching-bundles__bar-title {
  font-size: 0.875rem !important;
  font-weight: 400 !important;
  color: inherit !important;
  text-align: center !important;
}

.kaching-bundles__bar-subtitle {
  font-size: 0.75rem !important; /* 12px */
  line-height: 1rem !important; /* 16px */
  font-weight: 500 !important;
  color: var(--color-muted-foreground) !important;
  text-align: center !important;
  margin-top: 0.375rem !important;
}

/* Hide original titles and labels, replace with custom content */
/* .kaching-bundles__bar-title,
.kaching-bundles__bar-label,
.kaching-bundles__bar-subtitle {
  display: none !important;
} */

/* Hide original labels */
.kaching-bundles__bar-label {
  display: none !important;
}

/* Create a custom titles */
/* .kaching-bundles__bar:first-child .kaching-bundles__bar-first-line::before {
  content: "1 Pack" !important;
  display: block !important;
  font-size: 0.875rem !important;
  font-weight: 500 !important;
  color: inherit !important;
}

.kaching-bundles__bar:nth-child(2) .kaching-bundles__bar-first-line::before {
  content: "2 Packs" !important;
  display: block !important;
  font-size: 0.875rem !important;
  font-weight: 500 !important;
  color: inherit !important;
}

.kaching-bundles__bar:nth-child(3) .kaching-bundles__bar-first-line::before {
  content: "3 Packs" !important;
  display: block !important;
  font-size: 0.875rem !important;
  font-weight: 500 !important;
  color: inherit !important;
} */

.kaching-bundles__bar-pricing {
  align-items: center !important;
  margin: 0 !important;
}

.kaching-bundles__bar-price,
.kaching-bundles__bar-full-price {
  display: block !important;
}

/* Create a custom subtitles */
/* .kaching-bundles__bar:first-child .kaching-bundles__bar-pricing::after {
  content: "1 Month Supply" !important;
  display: block !important;
  font-size: 0.75rem !important;
  font-weight: 500 !important;
  color: inherit !important;
  margin-top: 0.5rem !important;
}

.kaching-bundles__bar:nth-child(2) .kaching-bundles__bar-pricing::after {
  content: "2 Months Supply" !important;
  display: block !important;
  font-size: 0.75rem !important;
  font-weight: 500 !important;
  color: inherit !important;
  margin-top: 0.5rem !important;
}

.kaching-bundles__bar:nth-child(3) .kaching-bundles__bar-pricing::after {
  content: "3 Months Supply" !important;
  display: block !important;
  font-size: 0.75rem !important;
  font-weight: 500 !important;
  color: inherit !important;
  margin-top: 0.5rem !important;
} */

/* === MOBILE (<=768px) === */
@media (max-width: 768px) {
  .kaching-bundles__bars {
    grid-template-columns: repeat(2, minmax(0, 1fr)); /* force 2-column max */
  }

  /* If exactly 3 items → allow 3 columns */
  .kaching-bundles__bar:first-child:nth-last-child(3),
  .kaching-bundles__bar:first-child:nth-last-child(3) ~ .kaching-bundles__bar {
    grid-column: span 1 !important;
  }
  .kaching-bundles__bars:has(
      .kaching-bundles__bar:first-child:nth-last-child(3)
    ) {
    grid-template-columns: repeat(3, minmax(0, 1fr));
  }

  /* If odd number (like 5) → last one centers */
  .kaching-bundles__bar:last-child:nth-child(odd) {
    grid-column: 1 / -1; /* span full row */
    justify-self: center; /* center horizontally */
  }
}
```

2nd Design
```css
/*! KACHING BUNDLES  */
.kaching-bundles__block-title {
  display: none !important;
}

.kaching-bundles__bars {
  display: grid !important;
  grid-template-columns: repeat(3, minmax(0, 1fr)) !important;
  gap: 0.5rem !important;
  justify-content: center !important; /* Helps centering in general */
  overflow: unset !important;
}

.kaching-bundles__bar {
  /* height: 6.25rem !important; */
  position: relative !important;
  background: white !important;
  box-shadow: none !important;
  margin: 0 !important;
}

.kaching-bundles__bar input[type="radio"] {
  display: none !important;
}

/* Card styling */
.kaching-bundles__bar-container {
  /* position: relative !important; */
  display: flex !important;
  height: 100% !important;
  background: white !important;
  border-radius: 4px !important;
  border: 1px solid var(--color-border) !important;
  flex-direction: column !important;
  padding-block: 1.5rem !important;
  padding-inline: 1rem !important;
  text-align: center !important;
  cursor: pointer !important;
  transition: all 0.2s ease !important;
  box-shadow: none !important;
}

.kaching-bundles__bar-container:hover {
  background-color: #e6e6e6 !important;
}

.kaching-bundles__bar--selected .kaching-bundles__bar-container {
  border: 1px solid #F2A92D !important;
  box-shadow: none !important;
}

.kaching-bundles__bar-wrapper {
  /* flex: 1 !important;
  display: flex !important;
  flex-direction: column !important; */
  padding: 0 !important;
  margin: 0 !important;
}

.kaching-bundles__bar-main {
  /* flex: 1 !important;
  display: flex !important;
  flex-direction: column !important; */
  padding: 0 !important;
  margin: 0 !important;
}

.kaching-bundles__bar-radio {
  display: none !important;
}

/* .kaching-bundles__bar-content {
  flex: 1 !important;
  display: flex !important;
  flex-direction: column !important;
  justify-content: space-between !important;
} */

.kaching-bundles__bar-content {
  position: absolute;
  inset: 0;
  display: grid !important;
  grid-template-columns: repeat(6, minmax(0, 1fr));
  grid-template-rows: repeat(14, minmax(0, 1fr));
}

/* To center titles and subtitles using "Vertical Style" */
/* .kaching-bundles__bar-content-left {
  align-items: center !important;
} */

.kaching-bundles__bar-content-left {
  position: absolute;
  inset: 0;
  display: grid !important;
  grid-template-columns: repeat(6, minmax(0, 1fr));
  grid-template-rows: repeat(14, minmax(0, 1fr));
}

.kaching-bundles__bar-title {
  font-size: 12px !important;
  line-height: 140% !important;
  font-weight: 400 !important;
  color: inherit !important;
  text-align: center !important;
}

@media (width >= 64rem) {
  .kaching-bundles__bar-title {
    font-size: 14px !important;
  }
}

.kaching-bundles__bar-subtitle {
  font-size: 12px !important; /* 12px */
  line-height: 140% !important; /* 16px */
  font-weight: 500 !important;
  color: var(--color-muted-foreground) !important;
  text-align: center !important;
  margin-top: 0.375rem !important;
}

@media (width >= 64rem) {
  .kaching-bundles__bar-subtitle {
    font-size: 10px !important;
  }
}

.kaching-bundles__bar-first-line {
  display: flex !important;
  align-items: center !important;
  justify-content: center !important;
  grid-column: 1 / -1;
  grid-row: 4;
}

.kaching-bundles__bar-label {
  position: absolute !important;
  top: -12% !important;
  /* left: 50% !important;
  transform: translateX(-50%) !important; */
  left: 50% !important;
  transform: translateX(-50%) !important;
  width: fit-content !important;
  font-size: 8px !important;
  line-height: 140% !important;
  white-space: nowrap !important;
  border-radius: 0.25rem !important; 
  padding-block: 0.25rem !important;
  padding-inline: 0.75rem !important;
}

@media (width >= 64rem) {
  .kaching-bundles__bar-label {
    font-size: 10px !important;
  }
}

/* "Most Popular" badge styling */
.kaching-bundles__bar:nth-child(2) .kaching-bundles__bar-label {
  color: #FFFFFF !important;
  background-color: #5FA885 !important;
  text-transform: uppercase !important;
}

/* "Best Deal" badge styling */
.kaching-bundles__bar:nth-child(3) .kaching-bundles__bar-label {
  color: #FFFFFF !important;
  background-color: #F2A92D !important;
  text-transform: uppercase !important;
}

.kaching-bundles__bar-second-line {
  display: flex !important;
  align-items: center !important;
  justify-content: center !important;
  grid-column: 1 / -1;
  grid-row: 10;
}

.kaching-bundles__bar-pricing {
  grid-column: 1 / -1;
  grid-row: 7;
  display: flex !important;
  flex-direction: row !important;
  align-items: center !important;
  justify-content: center !important;
  gap: 0.25rem !important;
  margin: 0 !important;
}

.kaching-bundles__bar-price,
.kaching-bundles__bar-full-price {
  display: block !important;
  font-size: 12px !important;
  line-height: 140% !important;
}

@media (width >= 64rem) {
  .kaching-bundles__bar-price,
  .kaching-bundles__bar-full-price {
    font-size: 14px !important;
    line-height: 140% !important;
  }
}
```

Event Listener (Optional)
```liquid
<sticky-bottom-container data-threshold="200">
  <div class="sticky-bottom__wrapper">
    <div class="sticky-bottom__product-info">
      <span class="sticky-bottom__title">{{ product.title }}</span>
      <div class="sticky-bottom__price-wrapper">
        <span class="sticky-bottom__price">{{ product.price | money }}</span>
        <span class="sticky-bottom__bundle-info" style="display: none;"></span>
      </div>
    </div>
    
    <atc-button>
      {% render 'button',
        button_size: 'icon',
        icon_mode: 'select',
        icon_select: 'plus',
        button_type: 'submit',
        button_form: product_form_id, 
      %}
    </atc-button>
  </div>
</sticky-bottom-container>

<script>
  (function() {
    let currentBundleData = null;
    
    // Get references to sticky bottom elements
    const stickyPrice = document.querySelector('.sticky-bottom__price');
    const bundleInfo = document.querySelector('.sticky-bottom__bundle-info');
    
    function updateStickyBottom(bundleData) {
      if (!stickyPrice || !bundleInfo) return;
      
      if (bundleData && bundleData.variantIdQuantities.length > 0) {
        // Calculate total quantity
        const totalQuantity = bundleData.variantIdQuantities
          .reduce((sum, item) => sum + item.quantity, 0);
        
        // Update the strikethrough price with fullPrice from bundle
        if (bundleData.pricing && bundleData.pricing.fullPrice) {
          stickyPrice.textContent = bundleData.pricing.fullPrice.formatted;
        }
        
        // Update bundle info display with discounted price
        const bundleText = `Bundle Deal: ${totalQuantity} item${totalQuantity > 1 ? 's' : ''} • ${bundleData.formattedPrice}`;
        
        bundleInfo.textContent = bundleText;
        bundleInfo.classList.add('active');
        stickyPrice.classList.add('has-bundle');
        
        console.log('✅ Sticky bottom updated with bundle:', {
          totalQuantity,
          fullPrice: bundleData.pricing.fullPrice.formatted,
          discountedPrice: bundleData.formattedPrice,
          variants: bundleData.variantIdQuantities
        });
      } else {
        // Reset to default - restore original product price
        stickyPrice.textContent = '{{ product.price | money }}';
        bundleInfo.textContent = '';
        bundleInfo.classList.remove('active');
        stickyPrice.classList.remove('has-bundle');
        
        console.log('ℹ️ Sticky bottom reset to default');
      }
    }
    
    // Listen for Kaching bundle events
    document.addEventListener('variants-changed', (event) => {
      console.log('🎯 Bundle variants changed:', event.detail);
      currentBundleData = event.detail;
      updateStickyBottom(currentBundleData);
    });
    
    document.addEventListener('deal-bar-selected', (event) => {
      console.log('🎯 Deal bar selected:', event.detail);
    });
    
    // Optional: Listen for deal deselection
    document.addEventListener('deal-bar-deselected', (event) => {
      console.log('🎯 Deal bar deselected');
      currentBundleData = null;
      updateStickyBottom(null);
    });
    
    // Initialize on load
    document.addEventListener('DOMContentLoaded', function() {
      console.log('🚀 Kaching Sticky Bottom Integration Ready');
    });
  })();
</script>
```
