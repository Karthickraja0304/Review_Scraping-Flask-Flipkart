# Review_Scraping-Flipkart
This Application is an automation project,that generates user reviews on a particular product from Flipkart. This application uses FlaskAPI.

## Working process:
### Searching a product on flipkart:
* To the search url of the website, concatenate the product to be searched. 

* urllib.request gets webpage from a particular url. Apply this function on search url
        
        webpage = urlopen(url) #generates webpage from url #from urllib.request import urlopen

* read() reads the entire webpage and returns the content. This includes all items from webpage, but we are only in need of html content.

        web_content = webpage.read()

* BeautifulSoup from bs4 helps extracting HTML content. Thus, filtering out all unwanted content.

        from bs4 import BeautifulSoup as bs
        web_html_content = bs(web_content, "html.parser")

* findAll() returns list of contents by taking in tags from which further web page link can be extracted. This list includes content which contains url of individual search result.

        list_of_contents = web_html_content.findAll('div', {"class":"_1AtVbE col-12-12"}) # extracts contents from with "class" as "_1AtVbE col-12-12" 

* Access the url of individual search result and apply requests.get(). It helps in extracting contents from url. Use 'utf-8' encoding and apply BeautifulSoup and findAll().

        content = requests.get(url)
        content.encoding='utf-8'
        prod_html = bs(content.text, "html.parser")
        list_of_product_reviews = prod_html.find_all('div', {'class': "_16PBlm"})

* Perform looping operation to extract info from all product reviews

                    reviews = []
            for commentbox in list_of_product_reviews:
                try:
                    name = commentbox.div.div.find_all('p', {'class': '_2sc7ZR _2V5EHH'})[0].text
                except:
                    name = 'No Name'

                try:
                    rating = commentbox.div.div.div.div.text
                except:
                    rating = 'No Rating'

                try:
                    commentHead = commentbox.div.div.div.p.text

                except:
                    commentHead = 'No Comment Heading'
                
                try:
                    comtag = commentbox.div.div.find_all('div', {'class': ''})
                    custComment = comtag[0].div.text
                except Exception as e:
                    print("Exception while creating dictionary: ",e)

                mydict = {"Product": searchString, "Name": name, "Rating": rating, "CommentHead": commentHead,
                          "Comment": custComment}
                reviews.append(mydict)

* store the reviews on a csv file

        with open(f'{searchString}.csv','w', encoding="utf-8") as csvfile:
            writer = csv.DictWriter(csvfile, fieldnames=mydict.keys())
            writer.writeheader()
            writer.writerows(reviews)
