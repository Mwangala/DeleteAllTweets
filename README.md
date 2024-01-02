How to delete all Twitter tweets. 

S/O to ChatGPT for optimizing the code LOL
### How to:
- Open Chrome and go to your twitter profile (https://twitter.com/USERNAME/with_replies)
- Open Chrome Developer Console.
- Type 'allow pasting' and hit enter
- Copy and Paste the code below into the console

```
const SELECTORS = {
  TWEET: '[data-testid="tweet"]',
  CARET: '[data-testid="caret"]',
  MENU_ITEM: '[role="menuitem"]',
  CONFIRM_SHEET_CONFIRM: '[data-testid="confirmationSheetConfirm"]',
  UNRETWEET_CONFIRM: '[data-testid="unretweetConfirm"]',
  UNRETWEET: '[data-testid="unretweet"]'
};

const DELAY_TIMES = {
  SHORT: 250,
  LONG: 3000
};

const deleteAllTweets = async () => {
  const processedButtons = new Set();
  const getDeleteButtons = () => Array.from(document.querySelectorAll(`${SELECTORS.TWEET} ${SELECTORS.CARET}`));
  const delay = (ms) => new Promise(resolve => setTimeout(resolve, ms));

  while (true) {
    const deleteButtons = getDeleteButtons().filter(button => !processedButtons.has(button));
    if (deleteButtons.length === 0) break;

    for (const button of deleteButtons) {
      try {
        processedButtons.add(button);
        button.click();
        await delay(DELAY_TIMES.SHORT);

        const menuItems = Array.from(document.querySelectorAll(SELECTORS.MENU_ITEM));
        const deleteOption = menuItems.find(item => item.textContent === 'Delete');

        if (deleteOption) {
          deleteOption.click();
          document.querySelector(SELECTORS.CONFIRM_SHEET_CONFIRM)?.click();
          await delay(DELAY_TIMES.LONG);
        } else {
          const tweetContainer = button.closest(SELECTORS.TWEET);
          const unretweetButton = tweetContainer?.querySelector(SELECTORS.UNRETWEET);

          if (unretweetButton) {
            unretweetButton.click();
            await delay(DELAY_TIMES.SHORT);
            document.querySelector(SELECTORS.UNRETWEET_CONFIRM)?.click();
            await delay(DELAY_TIMES.LONG);
          }
        }
      } catch (error) {
        console.error('An error occurred:', error);
      }
    }
  }

  console.log('All tweets deleted successfully!');
};

deleteAllTweets();
