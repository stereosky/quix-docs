# Data store for testing 

!!! danger "Legacy feature"

	This feature is not available to new users. However, legacy users may still have access to this functionality.

Quix provides a data store for testing and debugging purposes.

While [topics](../kb/glossary.md#topic) do provide a configurable retention time, persisting data into a database provides advantages - for example, you can perform powerful queries to retrieve historical data. This data can be retrieved and displayed using the Data Explorer, or retrieved using the [Query API](../apis/query-api/overview.md).

Quix provides a very simple way to persist data in a topic. Simply locate the topic in your topic list, and click the `Persistence` button. 

!!! important

    You don't have to use the Quix data store. Quix provides numerous [connectors](../connectors/index.md) for common database technologies, so you can always store your data in the database of your choice.

## Replay service

!!! danger "Legacy feature"

	This feature is not available to new users. However, legacy users may still have access to this functionality.

When data has been persisted, you have the option to not only query and display it, but replay it into your pipeline. This can be very useful for testing and debugging pipelines using historical data.

See how to [use the Quix replay service](../manage/replay.md).

See also an in-depth blog post on [stream reprocessing](https://quix.io/blog/intro-stream-reprocessing-python/){target=_blank}.
