## Notes on programming with the object of class `move2`

Currently, the `move2::move2_loc` is the most common input/output (IO) type in MoveApps. This IO type is an object of class `move2` which is restricted to only contain location events. The `move2` object allows also to contain non location events, and a mix of both. The IO type `move2::move2_nonloc` is also a object of class `move2` restricted to only contain non location events. The following applies to both IO types as both are objects of class `move2`.

The `move2` object flexibly accepts any column to be defining the timestamps or the track IDs. To avoid downstream errors, please do not use hardcoded column names for timestamps and track IDs within your Apps, but use the available functions to retrieve this information:

-   `mt_time()`: returns the timestamps for each event in the track
-   `mt_track_id()`: returns a vector of the track id associated to each event
-   `mt_time_column()`: returns the name of the column containing the timestamps used by the `move2` object
-   `mt_track_id_column()`: returns the name of the column containing the track IDs used by the `move2` object
- `sf::st_coordinates()`: returns the coordinates of the events from the track(s) of a `move2` object

To get an overview of the structure and a detailed explanation of the `move2` object please check out this vignette: [Programming with a move2 object](https://bartk.gitlab.io/move2/articles/programming_move2_object.html). At the end of this vignette you can also find a list of the most important functions to extract information of the `move2` object and other useful functions.

[Here](https://bartk.gitlab.io/move2/reference/index.html) you can find an overview of all the functions that are currently available in the `move2` R package. 

In the [`move2` website](https://bartk.gitlab.io/move2/index.html) there are several vignettes (Articles) with different examples that might provide you with ideas how the `move2` object can be manipulated.

### Apps using information of the track data table

If you are using information of the track data table in your App, take into account that sometimes columns of this table can be of class `list`. This can happen e.g. when tracks are merged using `mt_stack`, or when the track ID is changed (`mt_set_track_id`) and merges tracks. If the merged tracks have e.g. different values in the columns e.g. "age" and "weight", these values will be combined into a list in the resulting merged track.
This might happen in your App, but can also come from a manipulation in a previously used App. Please account for this possibility in your App (if relevant) to avoid unnecessary errors.

Here an example of an object containing a list in the track data table:
``` 
R

library("move2")
library("dplyr")

## creating 2 subsets of 2 tracks
dt1 <- mt_sim_brownian_motion(t = 1:7,tracks=c("indv_A","indv_B"))
dt1 <- mt_set_track_data(dt1, as_tibble(mt_track_data(dt1)))
dt2 <- mt_sim_brownian_motion(t = 8:10,start_location = c(1, 1),tracks=c("indv_A","indv_B"))
dt2 <- mt_set_track_data(dt2, as_tibble(mt_track_data(dt2)))

## adding to each subset a different value for one track attribute
dt1 <- mutate_track_data(dt1, attrbX="w")
dt2 <- mutate_track_data(dt2, attrbX="t")

## merging the subsets into one track
dt3 <- mt_stack(dt1,dt2,.track_combine="merge_list")
mt_track_data(dt3)

# # A tibble: 2 × 2
#   track  attrbX   
#    chr   list   
# 1 indv_A chr [2]
# 2 indv_B chr [2]

mt_track_data(dt3)$attrbX

# [[1]]
# [1] "w" "t"
# 
# [[2]]
# [1] "w" "t"
```

Here one example how to deal with it if you need to flatten the list:

```
R

## coming soon!
```



