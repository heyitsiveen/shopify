Add this `css` to `assets/critical.css`.
```css
/* APPSTLE SUBSCRIPTION */
.appstle_subscription_wrapper {
  display: grid !important;
  grid-template-columns: repeat(2, minmax(0, 1fr)) !important;
  gap: 1rem !important;
  border: 0 !important;
  border-radius: 0 !important;
  margin-bottom: 1rem !important;
}

.appstle_subscription_wrapper_option {
  font-family: var(--font-body--family) !important;
  font-size: 1rem !important;
  line-height: 140% !important;
  border-radius: 0 !important;
  border: 1px solid var(--color-primary-background) !important;
  padding-block: 1.25rem !important;
  padding-inline: 1rem !important;
}

.appstle_subscription_wrapper_option.appstle_include_dropdown {
  display: flex !important;
  flex-direction: column !important;
  align-items: center !important;
  justify-content: center !important;
  gap: 1rem !important;
  margin: 0 !important;
}

.appstle_subscription_radio_wrapper {
  display: flex !important;
  flex-direction: column !important;
  align-items: center !important;
  justify-content: center !important;
}

.appstle_one_time_details_wrapper {
  display: flex !important;
  flex-direction: column !important;
  align-items: center !important;
  justify-content: center !important;
  gap: 1rem !important;
}

.appstle_radio_label {
  display: flex !important;
  flex-direction: column !important;
  gap: 0.75rem !important;
}

.appstle-text-and-badge-wrapper {
  display: flex !important;
  flex-direction: column !important;
  align-items: center !important;
  justify-content: center !important;
  gap: 0 !important;
}

.appstle_one_time_details_wrapper .appstle_radio_label .appstle_circle {
  width: 1.5rem !important;
  height: 1.5rem !important;
  border: 1px solid var(--color-primary-background) !important;
  margin: 0 !important;
}

#appstle_subscription_widget0 .appstle_circle {
  width: 1.5rem !important;
  height: 1.5rem !important;
  border: 1px solid var(--color-primary-background) !important;
  margin: 0 !important;
}

.appstle_dot {
  width: 1rem !important;
  height: 1rem !important;
  background: var(--color-primary-background) !important;
}

#appstle_subscription_widget0 .appstle_dot {
  width: 1rem !important;
  height: 1rem !important;
  background: var(--color-primary-background) !important;
}

.appstle_one_time_price_wrapper {
  margin: 0 !important;
  text-align: center !important;
}

.appstle_subscription_amount_wrapper {
  margin: 0 !important;
  text-align: center !important;
}

.appstle_subscription_amount.transcy-money {
  color: var(--color-primary-background) !important;
}

.appstle_subscription_compare_amount.transcy-money {
  color: var(--color-muted-foreground) !important;
  font-size: 0.75rem !important;
  line-height: 140% !important;
  text-decoration: line-through !important;
}

.appstle_subscription_wrapper_option.appstle_include_dropdown .appstle_bundle_discount_amount {
  display: none !important;
}

.appstle_subscription_wrapper_option.appstle_include_dropdown .appstle_subscription_description_text {
  display: none !important;
}

.appstle_subscribe_option {
  margin: 0 !important;
}
```
