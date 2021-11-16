    # Sending-email-using-API-data
    Sending an email to notify is the international space station is within your area at night time

    import requests
    from datetime import datetime
    import smtplib
    import time

    my_email = "XYZ"
    password = "XYZ"

    MY_LAT = 51.570400
    MY_LONG = -0.315510


    def is_iss_overhead():
        response = requests.get(url="http://api.open-notify.org/iss-now.json")
        response.raise_for_status()
        data = response.json()

        iss_latitude = float(data["iss_position"]["latitude"])
        iss_longitude = float(data["iss_position"]["longitude"])

        if MY_LAT-5 <= iss_latitude <= MY_LAT+56 and MY_LONG-5 <= iss_longitude <= MY_LONG+5:
            return True


    def is_it_dark():
        parameters = {
            "lat": MY_LAT,
            "lng": MY_LONG,
            "formatted": 0,
        }

        response = requests.get("https://api.sunrise-sunset.org/json", params=parameters)
        response.raise_for_status()
        data = response.json()
        sunrise = int(data["results"]["sunrise"].split("T")[1].split(":")[0])
        sunset = int(data["results"]["sunset"].split("T")[1].split(":")[0])

        time_now = datetime.now()

        if time_now.hour >= sunset or time_now.hour <= sunrise:
            return True


    while True:
        time.sleep(60)
        if is_iss_overhead() and is_it_dark():
            with smtplib.SMTP("smtp.gmail.com") as connection:
                connection.starttls()
                connection.login(user=my_email, password=password)
                connection.sendmail(from_addr=my_email,
                                    to_addrs="XYZ",
                                    msg=f"Subject:The ISS is OVERHEAD!\n\nLOOK UP! THE ISS IS OVERHEAD!")
