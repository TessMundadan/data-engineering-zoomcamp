## Question 1
 --rm 

## Question 2 
wheel      0.43.0

## Question 3  Count records 

```
docker network create pg-network

docker run -it \
    -e POSTGRES_USER="root" \
    -e POSTGRES_PASSWORD="root" \
    -e POSTGRES_DB="ny_taxi" \
   -v /Users/tessmundadan/data-engineering-zoomcamp/2_docker_sql/ny_taxi_postgres_data:/var/lib/postgressql/data \
    -p 5432:5432 \
    --network=pg-network \
    --name=pg-database \
    postgres:13 -d

docker run -it \
    -e PGADMIN_DEFAULT_EMAIL="admin@admin.com" \
    -e PGADMIN_DEFAULT_PASSWORD="root" \
    -p 8080:80 \
    --network=pg-network \
    --name=pgadmin \
    dpage/pgadmin4 -d

```

Ingestion script in jupyter notebook -> cohorts/2024/01-docker-terraform/upload_data_hw1.ipynb

SELECT count(*) FROM public.green_trip_data where 
date(lpep_pickup_datetime) ='2019-09-18'
and date(lpep_dropoff_datetime) ='2019-09-18'
--15612

## Question 4 Longest trip for each day

SELECT date(lpep_pickup_datetime) 
FROM green_trip_data where trip_distance in 
(select max(trip_distance) 
from green_trip_data);
--"2019-09-26"

## Question 5 Three biggest pick up Boroughs

SELECT "Borough"	
FROM green_trip_data as g
jOIN zone as z
ON g."PULocationID" = z."LocationID"
WHERE z."Borough" <> 'Unknown'
and date(lpep_pickup_datetime)  = '2019-09-18'
group by 1
having sum(g.fare_amount)> 50000
ORDER by sum(g.fare_amount) desc
limit 3

"Brooklyn"
"Manhattan"
"Queens"

## Question 6 Largest tip

SELECT 
pz."Zone"	
,dz."Zone"
	, max(g.tip_amount)
FROM green_trip_data as g
JOIN public.zone as pz
ON g."PULocationID" = pz."LocationID"
JOIN public.zone as dz
ON g."DOLocationID" = dz."LocationID"

WHERE pz."Zone" = 'Astoria'
AND date(g.lpep_pickup_datetime) between '2019-09-01' And '2019-09-30'
GROup by 1,2
Order by 3 desc
limit 1

--"Astoria"	"JFK Airport"	62.31

## Question 7 Creating Resources

- In VM , install terraform.

- The vm should have the key to identify the service account.
You can sftp from local machine to VM

- create main.tf file 

- Run the script
terraform init
terraform plan 
terraform apply

```
(base) tessmundadan@de-zoomcamp:~/terraform_hw$ terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions
are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # google_bigquery_dataset.hw_dataset will be created
  + resource "google_bigquery_dataset" "hw_dataset" {
      + creation_time              = (known after apply)
      + dataset_id                 = "hw_dataset"
      + default_collation          = (known after apply)
      + delete_contents_on_destroy = false
      + effective_labels           = (known after apply)
      + etag                       = (known after apply)
      + id                         = (known after apply)
      + is_case_insensitive        = (known after apply)
      + last_modified_time         = (known after apply)
      + location                   = "US"
      + max_time_travel_hours      = (known after apply)
      + project                    = "majestic-hybrid-419715"
      + self_link                  = (known after apply)
      + storage_billing_model      = (known after apply)
      + terraform_labels           = (known after apply)
    }

  # google_storage_bucket.terra-hw will be created
  + resource "google_storage_bucket" "terra-hw" {
      + effective_labels            = (known after apply)
      + force_destroy               = true
      + id                          = (known after apply)
      + location                    = "US"
      + name                        = "majestic-hybrid-419715-terra-hw-bucket"
      + project                     = (known after apply)
      + project_number              = (known after apply)
      + public_access_prevention    = (known after apply)
      + rpo                         = (known after apply)
      + self_link                   = (known after apply)
      + storage_class               = "STANDARD"
      + terraform_labels            = (known after apply)
      + uniform_bucket_level_access = (known after apply)
      + url                         = (known after apply)

      + lifecycle_rule {
          + action {
              + type          = "AbortIncompleteMultipartUpload"
                # (1 unchanged attribute hidden)
            }
          + condition {
              + age                    = 1
              + matches_prefix         = []
              + matches_storage_class  = []
              + matches_suffix         = []
              + with_state             = (known after apply)
                # (3 unchanged attributes hidden)
            }
        }
    }

Plan: 2 to add, 0 to change, 0 to destroy.

───────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take
exactly these actions if you run "terraform apply" now.
(base) tessmundadan@de-zoomcamp:~/terraform_hw$ terraform apply

Terraform used the selected providers to generate the following execution plan. Resource actions
are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # google_bigquery_dataset.hw_dataset will be created
  + resource "google_bigquery_dataset" "hw_dataset" {
      + creation_time              = (known after apply)
      + dataset_id                 = "hw_dataset"
      + default_collation          = (known after apply)
      + delete_contents_on_destroy = false
      + effective_labels           = (known after apply)
      + etag                       = (known after apply)
      + id                         = (known after apply)
      + is_case_insensitive        = (known after apply)
      + last_modified_time         = (known after apply)
      + location                   = "US"
      + max_time_travel_hours      = (known after apply)
      + project                    = "majestic-hybrid-419715"
      + self_link                  = (known after apply)
      + storage_billing_model      = (known after apply)
      + terraform_labels           = (known after apply)
    }

  # google_storage_bucket.terra-hw will be created
  + resource "google_storage_bucket" "terra-hw" {
      + effective_labels            = (known after apply)
      + force_destroy               = true
      + id                          = (known after apply)
      + location                    = "US"
      + name                        = "majestic-hybrid-419715-terra-hw-bucket"
      + project                     = (known after apply)
      + project_number              = (known after apply)
      + public_access_prevention    = (known after apply)
      + rpo                         = (known after apply)
      + self_link                   = (known after apply)
      + storage_class               = "STANDARD"
      + terraform_labels            = (known after apply)
      + uniform_bucket_level_access = (known after apply)
      + url                         = (known after apply)

      + lifecycle_rule {
          + action {
              + type          = "AbortIncompleteMultipartUpload"
                # (1 unchanged attribute hidden)
            }
          + condition {
              + age                    = 1
              + matches_prefix         = []
              + matches_storage_class  = []
              + matches_suffix         = []
              + with_state             = (known after apply)
                # (3 unchanged attributes hidden)
            }
        }
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

google_bigquery_dataset.hw_dataset: Creating...
google_storage_bucket.terra-hw: Creating...
google_storage_bucket.terra-hw: Creation complete after 1s [id=majestic-hybrid-419715-terra-hw-bucket]
google_bigquery_dataset.hw_dataset: Creation complete after 1s [id=projects/majestic-hybrid-419715/datasets/hw_dataset]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
(base) tessmundadan@de-zoomcamp:~/terraform_hw$ 
```
