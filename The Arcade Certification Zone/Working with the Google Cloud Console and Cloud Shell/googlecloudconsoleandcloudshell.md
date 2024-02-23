# Working with the Google Cloud Console and Cloud Shell

### first create buckect from lab
### then open cloud shell then paste this command to finish this lab


```bash
gcloud storage buckets create gs://$GOOGLE_CLOUD_PROJECT-2

echo "subscibe to quicklab" > text.txt

gcloud storage cp text.txt gs://$GOOGLE_CLOUD_PROJECT-2


```
