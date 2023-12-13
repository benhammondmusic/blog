---
title: "Shaving hours off a Pandas script"
datePublished: Thu Sep 14 2023 05:28:02 GMT+0000 (Coordinated Universal Time)
cuid: clmiqc3en000j09jqd7yr0elr
slug: shaving-hours-off-a-pandas-script
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1694667364082/8389465b-263f-475f-ab94-a7e8850caac1.jpeg
tags: python, pandas, rust, datavisualization, polars

---

## Crunching a few hundred million lines of data

At the [Health Equity Tracker](https://healthequitytracker.org/exploredata), the largest dataset we work with is a case-level set provided by the CDC with every COVID-19 infection in the United States, along with additional information including race, ethnicity, county, and other symptoms. The data is provided in zipped .csv files in a private GitHub repo (due to patient privacy) and contains several hundred million rows across over a dozen files. Initially, our tracker, as coded by Google.org provided only the cumulative "snapshot" of the current rates of disease by race, age, or sex, down to the county level.

[![Choropleth map from the Health Equity Tracker showing the United States, with states and territories colored from dark green to yellow representing cumulative COVID rates in each state](https://cdn.hashnode.com/res/hashnode/image/upload/v1694668435759/03be141e-0b64-4cde-8671-ca840b25b819.png align="center")](https://healthequitytracker.org/exploredata?mls=1.covid-3.00&group1=All&dt1=covid_hospitalizations#rate-map)

Last year, however, in a major technical effort by our team at Morehouse School of Medicine and data visualization expert [Mia Svarvas](http://miaszarvas.com), we were able to implement time-tracking, whereby we could now aggregate and plot these disease rates across every month since the start of the COVID-19 pandemic.

[![Time-series line chart comparing monthly rates of White and Native American COVID hospitalizations since early 2020. The line for American Indian and Alaska Native is significantly higher than White at essentially every measured point in time  ](https://cdn.hashnode.com/res/hashnode/image/upload/v1694668563775/827ccfaf-99e1-4d7b-8d5e-eefb0029e80d.png align="center")](https://healthequitytracker.org/exploredata?mls=1.covid-3.00&group1=All&dt1=covid_hospitalizations#rates-over-time)

As the requirements grew, so did the time it took to run the aggregation script, which needed to be done locally by an authorized team member before uploading for further processing and calculations on Google Cloud Run. The CDC releases new datasets regularly, so this entire process would easily take an entire day of each month for a team member. Running locally on my 2021 MacBook Pro, it would take **nearly 3 hours to complete the aggregations**.

> With a few tweaks, I was able to reduce that 3-hour run-time to ~25 minutes!

## Improvements

I love that our codebase is open-source, so you can [check out the exact PR here](https://github.com/SatcherInstitute/health-equity-tracker/pull/2342), but below I'll outline the specific steps that sped things up so significantly. To benchmark, I ran the Python script against just a single raw .csv file (instead of all ~20).

> Time taken by the original code to process a single file: 3*13s*

Here are the incremental changes I made, and the improved speed measured from each step:

* Two major items: Using Pandas' built-in optimized (vectorized) functions inside our `combine_race_eth()` utility, rather than mapping/applying a regular Python lambda function against each row, and only reading in the relevant subset of columns with `usecols`: **191s**
    
* The above changes, plus removing some unused string manipulations meant to detect and remove empty quotes `""`: **180s**
    
* The above changes, plus using `chunk_size = 1_000_000`. This argument splits the data frame into virtual chunks of rows (in this case one million) so that your machine doesn't have to hold the entire thing in memory. Generally speaking, smaller and more numerous chunks will require less processing power but will require more time: **75s**
    
* Increasing `chunk_size` to 2 million: **64s**
    
* Increasing `chunk_size` to 5 million: **57s**
    
* At this point, I am functionally not using chunking, because the number of rows in the source data is less than the size of my chunk. However, I left it in place to future-proof against the CDC possibly shipping files with over 5 million rows
    

> When running against the full set of all raw files, the run time went from 2 hours and 40 minutes down to 34 minutes and produced identical output files: **Over 75% time savings!**

## Sanity Checks

Data integrity is of the utmost importance for us as a research institution, particularly one presenting information on underserved and marginalized populations. To ensure my refactor didn't cause unexpected results, I implemented a "sanity check", and renamed all of the existing .csv results (produced by the old code) with the suffix `_old,` then wrote a quick bash script that compared every line in the `_old` and newly refactored results:

* ```bash
            for new_file in cdc_restricted_by_*.csv; do
                old_file="old_$new_file"
                if diff -q "$old_file" "$new_file"; then
                    echo "Files $old_file and $new_file are identical."
                fi
            done
    ```
    

To "test the test" and confirm the check itself was working, I also ran this `diff` command to compare the `county_race` and `state_race` files, and observed hundreds of differences (as expected).

## Abandoned Optimizations

The most common suggestion I found when researching Panda's optimization was to utilize the vectorized methods built-in to Pandas, rather than using `.apply()` to apply a lambda or function against each row of the data frame iteratively. In some cases, like refactoring the `race` and `ethnicity` -&gt; `race/ethnicity` function, this did speed up the algorithm significantly. However, there were other vectorization optimizations I tried that surprisingly *slowed down* the script, making the problem worse! These were all `.str` Pandas methods, which while sometimes convenient are [a known subset](https://github.com/pandas-dev/pandas/issues/35864) of inefficient vectorized methods.

One example was dealing with our county [FIPS (Federal Information Processing System) codes](https://en.wikipedia.org/wiki/FIPS_county_code) which need to be treated as strings that can include leading zeros (for example Denver County FIPS is `08031`, which when treated as a number turns into `8,031`. You might have run into this issue in Microsoft Excel when inputting things like ZIP codes as well.

The type conversion and string formatting are done in the existing code using `.map()`:

```python
df[COUNTY_FIPS_COL] = df[COUNTY_FIPS_COL].map(lambda x: x.zfill(5) if len(x) > 0 else x)
```

and I tried using the built-in method to accomplish the same thing:

```python
df[COUNTY_FIPS_COL] = df[COUNTY_FIPS_COL].str.zfill(5) # actually slower 😿
```

Overall, any attempts at optimizations with the data frame `.str` methods were abandoned as they did not improve performance, and in some cases slowed it down significantly.

## Next steps

Although 25 minutes is far more manageable than multiple hours, there are several more steps I am looking into to further optimize this aggregation. If you are reading this and have insight into any of these techniques, let me know in the comments!

* 🧑‍💻**Parquet** **instead of** 🧑‍💻**CSV**: Using the CDC's provided `.parquet` files instead of the `.csv` files. From what I understand Parquet files are binary/machine-readable, so slightly more difficult to work with (you can't just peek at the contents in VSCode) but more efficient in terms of memory utilization. However, I'm not certain this would provide significant benefits, since each file is used briefly and never continuously opened and manipulated.
    
* **🐻‍❄️Polars instead of 🐼Pandas:** A more significant change, which would further complicate the codebase, is to introduce a new data frame library for this aggregation called Polars. Its usage is quite similar to Pandas, but it can stream and manipulate datasets that are larger than the machine's memory. When loading data into a "lazy frame" and performing various aggregations, filters, and calculations, the library is clever enough to only process the bits needed for the specified work. Although Polars is written in Rust, the library is available for both Rust and Python.
    
* 🦀**Rust** **instead of** 🐍**Python**: Maybe out of scope for the project, but of course the only option I've started pursuing (mainly because it was exciting to finally have a tech problem that Rust could help me solve and I wanted to check out what all the buzz was about). Since this aggregation is performed on the local development machine and is quite distinct from the rest of our Airflow data pipelines, I could refactor this entire script into Rust (using Polars as mentioned above).
    

Here's a little snippet of what I've got working. *Be kindly, ye Rustaceans!*

```rust
fn process_lazyframe_into_by_sex_df(lf: LazyFrame) -> Result<DataFrame, PolarsError> {
    let known_sex_groups = vec!["Male", "Female", "Other"];
    let know_sex_series = Series::new("KNOWN_SEX_GROUP", known_sex_groups);
    let is_known_sex_group = col("sex").is_in(lit(know_sex_series));

    let groupby_cols = vec![col("state_postal"), col("sex"), col("time_period")];

    let df = lf
        // "time_period" as cdc col with only YYYY-MM
        .with_column((col("cdc_case_earliest_dt").str().str_slice(0, Some(7))).alias("time_period"))
        // count every row as 1 case
        .with_column(col("time_period").is_not_null().alias("cases"))
        .with_column(col("hosp_yn").eq(lit("Yes")).alias("hosp_y"))
        .with_column(col("hosp_yn").eq(lit("No")).alias("hosp_n"))
        .with_column(
            col("hosp_yn")
                .neq(lit("Yes"))
                .and(col("hosp_yn").neq(lit("No")))
                .alias("hosp_unknown"),
        )
        .with_column(col("death_yn").eq(lit("Yes")).alias("death_y"))
        .with_column(col("death_yn").eq(lit("No")).alias("death_n"))
        .with_column(
            col("death_yn")
                .neq(lit("Yes"))
                .and(col("death_yn").neq(lit("No")))
                .alias("death_unknown"),
        )
        // only keep Male/Female/Other/Unknown options for sex
        .with_column(
            when(is_known_sex_group)
                .then(col("sex"))
                .otherwise(lit("Unknown")),
        )
        // only keep known postal codes; combine rest as "Unknown" for national numbers
        .with_column(
            when(
                col("res_state")
                    .is_null()
                    .or(col("res_state").eq(lit("Missing")))
                    .or(col("res_state").eq(lit("Unknown")))
                    .or(col("res_state").eq(lit("NA"))),
            )
            .then(lit("Unknown"))
            .otherwise(col("res_state"))
            .alias("state_postal"),
        )
        .groupby(groupby_cols)
        .agg(vec![
            col("cases").sum(),
            col("hosp_y").sum(),
            col("hosp_n").sum(),
            col("hosp_unknown").sum(),
            col("death_y").sum(),
            col("death_n").sum(),
            col("death_unknown").sum(),
        ])
        .collect();

    df
}
```

So far I've gotten it to the point that it:

* Loads every .csv file from a particular directory into memory
    
* Combines them into a LazyFrame
    
* Performs the column calculations, manipulations, and aggregations needed for the `SEX` table generations and the produced tables are identical to the full tables produced by our Python code
    

Run time is well under 5 minutes! Of course, that's only for a single demographic breakdown, so it's still not clear if my super newbie Rust code will truly save time. I'll follow up once I've had a chance to finish, for now the [repo is available on my GitHub](https://github.com/benhammondmusic/rust-big-data). Overall, I've enjoyed messing around in Rust (it feels like when I was learning TypeScript, except I can't just cheat and use `any` to force things to work when the compiler yells!). And importantly, I've freed up a lot of free time for our small team, allowing us to work on new features for the tracker and [waste less time on the waiting for code to run](https://xkcd.com/303/).