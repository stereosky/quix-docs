# 6. Stream merge

In this part of the tutorial you add a stream merge service into the pipeline. This service merges the inbound streams into one outbound stream. This is required because the images from each traffic camera are published to a different stream, allowing the image processing services to be scaled up if needed. Once all the image processing is completed and to allow the UI to easily use the data generated by the processing stages, the data from each stream is merged into one stream.

Follow these steps to deploy the **Stream merge service**:

1.  Navigate to the `Code Samples` and locate `Stream merge`.

2.  Click `Deploy`.

3.  And again, click `Deploy`.

    This service receives data from the `image-processed` topic and streams data to the `image-processed-merged` topic.

??? example "Understand the code"

    Here's the code in the file `quix_function.py`:

    ```python
    # Callback triggered for each new event. (1)
    def on_event_data_handler(self, stream_consumer: qx.StreamConsumer, data: qx.EventData):
        print(data.value)

        # All of the data received by this event data handler is published to the same predefined topic (2)
        self.producer_topic.get_or_create_stream("image-feed").events.publish(data)

    # Callback triggered for each new parameter data. (3)
    def on_dataframe_handler(self, stream_consumer: qx.StreamConsumer, df: pd.DataFrame):

        # Add a tag for the parent stream (4)
        df["TAG__parent_streamId"] = self.consumer_stream.stream_id

        # add the base64 encoded image to the dataframe (5)
        df['image'] = df["image"].apply(lambda x: str(base64.b64encode(x).decode('utf-8')))

        # All of the data received by this dataframe handler is published to the same predefined topic (6)
        self.producer_topic.get_or_create_stream("image-feed") \
            .timeseries.buffer.publish(df)
    ```

    1. `on_event_data_handler` handles each new event on the topic that is subscribed to.
    2. All events are published to the output topic in a single stream called `image-feed`.
    3. `on_dataframe_handler` handles each new dataframe or timeseries data on the topic that is subscribed to.
    4. Add a tag to preserve the parent stream id.
    5. Add an `image` column to the dataframe and set the value to the base64 encoded image.
    6. All data is published to the output topic in a single stream called `image-feed`.

[Part 7 - Web UI :material-arrow-right-circle:{ align=right }](web-ui.md)