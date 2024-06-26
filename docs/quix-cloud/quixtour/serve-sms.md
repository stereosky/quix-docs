# Serve - send an SMS alert

In this part of the tour you'll learn how to create a simple destination. This destination sends an SMS alert to a systems administrator when a CPU spike data frame arrives.

## Prerequisites

If you've completed this tutorial so far, you should have all the prerequisites already installed.

**Optionally:** You can sign up for a [free Vonage account](https://developer.vonage.com/sign-up), to be able to send an SMS. If you would like to try this, simply set `SEND_SMS = True` in the `main.py` code you create later, to switch this feature **on**.

## Step 1: Create the destination

To create the SMS alert destination:

1. In your `Develop` environment, click on `Code Samples` in the main left-hand navigation. 
2. Select the `Python`, `Destination`, and `Basic templates` filters.
3. For `Starter destination` click `Preview code`.
4. Click `Edit code`.
5. Name the destination "CPU Alert SMS".
6. Select the input topic `cpu-spike`.
7. In the project view click on `main.py` to edit it.
8. Replace all the code in `main.py` with the following:

    ``` python
    from quixstreams import Application
    import os

    # Set this to True if you want to actually send an SMS (you'll need a free Vonage account)
    SEND_SMS = False 

    if SEND_SMS:
        # Configure Vonage API
        # add vonage module to requirements.txt to pip install it
        import vonage 
        vonage_key = os.environ["VONAGE_API_KEY"]
        vonage_secret = os.environ["VONAGE_API_SECRET"]
        to_number = os.environ["TO_NUMBER"]

        client = vonage.Client(key=vonage_key, secret=vonage_secret)
        sms = vonage.Sms(client)

    def send_sms(message):
        """
        Send an SMS using Vonage API
        """
        print("Sending SMS message to admin...")
        response_data = sms.send_message(
            {
                "from": "Vonage APIs",
                "to": to_number,
                "text": message,
            }
        )

        if response_data["messages"][0]["status"] == "0":
            print("Message sent successfully.")
        else:
            print(f"Message failed with error: {response_data['messages'][0]['error-text']}")
        return

    def send_alert(row):
        """
        Trigger a CPU spike alert and send an SMS notification
        """
        if SEND_SMS:
            send_sms(row)
    
    # Create an Application
    # It will get the SDK token from environment variables to connect to Quix Kafka
    app = Application()

    # Define an input topic
    input_topic = app.topic(os.environ["input"])
    
    # Create a StreamingDataFrame to process data
    sdf = app.dataframe(input_topic)

    # Trigger the "send_alert" function for each incoming message
    sdf = sdf.update(send_alert)

    # Print messages to the console
    sdf = sdf.update(lambda row: print(row))

    if __name__ == "__main__":
        # Run the Application
        app.run(sdf)    
    ```

## Step 2: Send an SMS (optional)

This section is **optional**. 

If you want to send an alert SMS follow these steps:

1. Change the variable `SEND_SMS` to `True` in your `main.py`.
2. In the `Environment variables` panel, click `+ Add`. The `Add Variable` dialog is displayed. 
3. Complete the information for the following environment variables (you obtain these from your Vonage developer dashboard):

    | Variable name | Variable type |
    |----|----|
    | VONAGE_API_KEY | `secret` |
    | VONAGE_API_SECRET | `secret` |
    | TO_NUMBER | `secret` |
               
    See also [how to add environment variables](../../deploy/environment-variables.md).

4. You now need to add the `vonage` module to the `requirements.txt` file in your project. Click to open it and add a line for `vonage`. This ensures the module is built into the deployment.

## Step 3: Tag and deploy your SMS alert service

You can now tag and deploy your code:

1. Tag the project as `sms-v1` and deploy as a service (watch the video if you're not sure how to do this).
2. Monitor the logs for the deployed process.

## Step 4: Generate an alert

Again generate a CPU spike by opening several large applications on your laptop. If you have SMS alert enabled, you'll receive an SMS. If not, you can check the logs.

## Conclusion

You've now completed the Quix Cloud Tour. You've built a simple but complete stream processing pipeline. You can reuse the code, with some modifications, in your own projects. With a small amount of work, the SMS service you created could be turned into a general purpose SMS alaerting module, using the Vonage APIs. It could also be adapted to build out other alerting services, such as [PagerDuty](../../tutorials/influxdb-alerting/add-alerting.md).

## Next steps

To continue your Quix learning journey, you may want to consider some of the following resources:

* [Quix Streams docs](https://quix.io/docs/quix-streams/introduction.html)
* [InfluxDB alerting tutorial](../../tutorials/influxdb-alerting/overview.md)
